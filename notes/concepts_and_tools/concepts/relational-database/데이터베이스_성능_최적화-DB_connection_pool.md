## DBCP(DB Connection Pool)란? 성능에 도움이 되는 이유?

    _backend server가 데이터베이스 서버와 통신할 때 TCP 프로토콜을 사용한다. 근데 이건 매번 연결의 시작과
    끝에 open connection, close connection 해야하는데, query의 수행은 둘째치고 이 비용이 크다._

    _그래서 API 서버가 DB서버에 미리 connection 인스턴스를 여러개 만들어 놓고, connection pool로 활용하는
    것이다. API서버에서는 connection pool에서 미리 생성해둔 connection을 갖다쓰고 또 반납한다._


## DBCP 관련 설정 팁

    ### DB 서버 설정

        _(아래 내용은 DBMS마다 차이다 있다. 아래는 mysql 기준)_

        - max_connections

            client와 맺을 수 있는 최대 connection 수. 너무 많으면 리소스를 많이 먹고, 너무 적으면 서비스가
            활용할 DBCP이 그만큼 작다.

        - wait_timeout

            client는 때떄로 connection을 비정상 종료하거나 유기할 수 있다. 이럴 때 DB서버가 하염없이 기다리다보면
            이런 유기된 connection들이 쌓여서 서버 리소를 많이 낭비할 수 있다.

            이를 대비하여, connection이 inactive 할 때 얼마나 기다린 뒤에 스스로 close 할지 설정하는 것.

            * 시간 내 요청이 도착하면 0초부터 다시 카운트


    ### DB 클라이언트(backend API server) 설정

        _(아래 내용은 DB client library 마다 차이다 있다. 아래는 HikariCP 기준)_

        - maximumPoolSize

            pool에서 최대로 유지하는 connection 수. idle + active(aka. in-use)

        - minimumIdle

            pool에서 최소한 유지하는 idle connection 수.

        - maxLifetime

            connection이 pool에 유지되는 최대 시간. 이 시간이 지나면 connection은 pool에서 제거된다. 만약,
            상태가 idle인 경우 즉시, active인 경우는 pool로 반환된 후 제거된다.

            주의할 점은, active인 경우 암만 기다려도 pool에 반환 안되면 제거가 안되는데, 이러면 DB server에서
            connection time limit에 따라 connection이 사용 불능 상태가 될 수 있다. 그러니까 잘 관리해줘야 한다.

            또 maxLifetime은 DB 서버의 connection time limit보다 **몇 초 짧게** 설정하는 것이 좋다. 네트워크
            통신 지연을 고려해보면, client에서 송신한 query가 도착 전에 connection time limit이 끝나고
            connection이 끊기는 edge case가 있을 수 있기 때문이다.

            * 이 시간이 너무 짧으면, connection을 매번 새로 만들어야 하므로 성능이 떨어질 수 있다.
            * 이 시간이 너무 길면, connection이 유기되어 있을 수 있다.

        - connectionTimeout

            pool에서 connection을 빌려오기 위해 최대로 기다리는 시간. request가 너무 많아서 connection
            pool에 대기열이 너무 길면 수십초를 기다릴 수도 있다. 유저 입장에서 주구장창 기다리면 짜증나니까
            차라리 '요청이 넘 많아서 감당이 안되니 다음에 다시 시도하세요.'라고 에러를 띄우는 편이 나은 것.


## 어떻게 적절한 connection 설정을 할 수 있을까?

    일반적으로 DB 서버는 replica를 두어서 운영한다. read-write을 수행하는 master와 read-only를 수행하는
    slave 두어개?

    이럴 때,

        1. 모니터링 환경 구축
            - 서버 리소스, 스레드 수
            - DBCP connection pool 상태
            - 등등

        2. 백엔드(api server && db server) 시스템 부하 테스트 (with nGrinder 등.. )
            - request per second
            - average response time
            - error rate
            - 등등

    위 과정을 통해 변곡점을 찾아내고, 이를 개선할 필요를 따지고(비지니스 관점), 원인을 찾고 개선 방안을
    도출하는 것의 반복이다.

    * reference: [네이버D2 블로그 "Commons DBCP 이해하기"](https://d2.naver.com/helloworld/5102792)
    * reference: [youtube 쉬운코드](https://www.youtube.com/watch?v=zowzVqx3MQ4&list=PLcXyemr8ZeoREWGhhZi5FZs6cvymjIBVe&index=30)
