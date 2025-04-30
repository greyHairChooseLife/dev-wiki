# 󰏢 개발 환경 - Skills



## Container [-] 

[docker](/Area/Programming/Skills/Container/docker)
[HUB: kubernetes](/Area/Programming/Skills/Container/kubernetes/index)


[-] 
[from docker to open-source](https://www.youtube.com/watch?v=Z5uBcczJxUY)


## Web Dev [-] 

[static vs dynamic website](/Area/Programming/Skills/Web_Dev/static_vs_dynamic_website)
[design](/Area/Programming/Skills/Web_Dev/design)


[-] 
[천재들이 만든 웹의 구조는 실제로 이렇게 동작합니다.](https://www.youtube.com/watch?v=po5_T3wSPJI)

[-] 
- 제대로 된 에러핸들링 전략이 있어야겠다.

  `try ... catch ...` 구문은 연쇄적으로 에러를 추적한다. cascade error handling이라고 부를 수 있겠다.
  따라서 (바깥쪽)가장 먼저 에러를 이르킨 지점부터 (안쪽)가장 깊은 지점까지 단계별로 에러를 추적하며
  에러메시지가 누적 로깅 된다. 이때 어떻게 연쇄적으로 throw시키고, 어떤 에러객체를 전달할지 정해진
  전략이 필요하다.

  - HTTP 상태코드
    | code | 이름                  | 설명                                           |
    |------|-----------------------|------------------------------------------------|
    | 401  | Unauthorized          | 인증이 필요하거나 실패했을 때.                 |
    | 403  | Forbidden             | 인증은 되었지만, 자원에 접근할 권한이 없을 때. |
    | 500  | Internal Server Error | 서버 내부 문제.                                |

[-] 2024-12-05
- What is preflight request?

  비표준 http 헤더를 사용하는 경우 보안을 위해 미리 요청을 보내본다는데... 이와 관련된 캐싱 정책도
  개발자가 고려해야할 상항이다. 관련된 내용을 자세히 알아두자.

[-] 2024-12-06
- typescript+express 서버에서 컨트롤러를 만들 때, early return으로 res객체를 보낼 때가 있다. 뭔가
에러가 있다거나 경우에 따라 다른 응답을 보내야 할 때다. 근데 해당 컨트롤러의 마지막까지 실행되지
않고 응답한다면, 이후의 코드는 실행되지 않도록 return 해줘야한다. 근데 이게 `res.status().json()`
자체를 리턴하는것과 이후 void를 리턴하는 것에 차이가 있다.(당연하다..) 근데 문제는 이게 다르다는게
아니라 일관되지 않다는거다. `return res.status().json()`을 하면 에러가 날 때가 있고, 아닐때가 있다.
typescript의 기본 request, response객체 타입에 어긋난게 에러의 원인이라는데, 에러의 원인과는 별개로
왜 일관되지 않은 결과가 나타나는가?

  - 사례
    ```typescript
    export const getFileSignedUrl = async (req: AuthRequest, res: Response) => {
      try {
        // 인증 확인
        if (!req.userInfo) {
          res.status(401).json({ message: '인증되지 않은 사용자입니다.' });
          return;
        }

        const fileKey = decodeURIComponent(req.params.fileKey);

        if (!fileKey) {
          res.status(400).json({ message: '파일 키가 필요합니다' });
          return;
        }

        // 파일 존재 여부 및 접근 권한 확인 로직 추가 필요
        const presignedUrl = await awsS3Service.getPresignedFileUrl(fileKey);

        res.json({ presignedUrl });
      } catch (error) {
        console.error('파일 서명 URL 생성 오류:', error);
        res.status(500).json({
          message: '파일 URL 생성 중 알 수 없는 오류가 발생했습니다.',
        });
      }
    };
    ```


## Chrome Extension

> [!lg] Log 2025-02-27
> surfingkeys라는걸 잘 쓰고있었는데, 최근 업데이트 후 문제가 생겨서 직접 버전을 다운그레이드
> 해야했다.
>
> [관련 이슈](https://github.com/brookhong/Surfingkeys/issues/2244)
>
> 타인이 배포하는 프로젝트를 내 맘대로 다운그레이드 할 수는 없고, 소스코드를 받아서 원하는 버전으로
> checkout 한 뒤, 직접 빌드했다.(원작자가 npm 스크립트를 미리 작성해둬서 편리했다.)
>
> 브라우저에서 제공하는 load unpacked 기능을 사용하니, 원하는대로 동작했다.
>
> 이런식으로 직접 빌드한 도구를 사용하면 더 간결하게 이것저것 만들 수 있겠다.


> [!rf]
> [따라하기: chrome todo extension in JS](https://www.youtube.com/watch?v=ny-L_KLrKIU)


## Cloud Computing

[AWS](/Area/Programming/Skills/Cloud_Computing/AWS)


## CI CD

_배포 자동화는 고객의 요구사항을 빠르게 반영하는 핵심 요소이다. 요구사항을 파악하고
빠르게 반영하기도 바쁜데, 배포가 매끄럽지 못해서 턱턱 막힌다면 진짜 갑갑하다._

[single container SPA](/Area/Programming/Skills/CI_CD/single_container_SPA)


## Languages [-] 

[Javascript](/Area/Programming/Skills/Languages/Javascript) [-] 
[Typescript](/Area/Programming/Skills/Languages/Typescript)
[Dart](/Area/Programming/Skills/Languages/Dart)

### Python

[기본 개념](/Area/Programming/Skills/Languages/Python/기본_개념)
[격리된 가상환경으로 개발 시작하기](/Area/Programming/Skills/Languages/Python/격리된_가상환경으로_개발_시작하기)

  > [!INFO]
  >
  > 파이썬은 널리 이용되는 만큼 생태계가 복잡하다. 따라서 아래 두가지가 중요하다.
  >   - 의존성 관리를 잘 해줘야한다.
  >   - 시스템 패키지를 해치지 않도록 주의해야한다.
  >
  > 따라서 나는 가상환경의 확실한 격리를 컨테이너를 활용하여 시도한다.


## Web Scraping [-] 

[general tips](/Area/Programming/Skills/Web_Scraping/general_tips)


[-] 
[Scrape Anything with DeepSeek V3 + Scraping Tool Integration (CHEAP & EASY)](https://www.youtube.com/watch?v=WkLdLJJzV1k&t=27s)


## Big Data

[Keywords](/Area/Programming/Skills/Big_Data/Keywords)
[jq 사용법](/Area/Programming/Skills/Big_Data/jq_사용법)


### REFERENCES

[free-ebook: "Data Wrangling with JavaScript"](https://www.manning.com/books/data-wrangling-with-javascript?a_aid=datawranglingwithjavascript&a_bid=acc654f9)
[빅데이터 플랫폼 특론: play-list](https://www.youtube.com/playlist?list=PLCsebpDZm0n6HYSDaNxKQYrNrD4Xk9meX)
[하둡 분산파일 시스템 구축: play-list](https://www.youtube.com/watch?v=g6xIMSYjh0w&list=PLY-_9hx4ldZwYOjtfRT0MV2k9JcnTUYW2)



## Database [-] 

### RDBMS

> [!rf]
> [강의 재생목록](https://www.youtube.com/playlist?list=PLcXyemr8ZeoREWGhhZi5FZs6cvymjIBVe)

[관계형 데이터베스의 종류](/Area/Programming/Skills/Database/RDBMS/관계형_데이터베스의_종류)
[기본개념 및 키워드](/Area/Programming/Skills/Database/RDBMS/기본개념_및_키워드)
[SQL 기본개념, Attribute Type, Key Constraints](/Area/Programming/Skills/Database/RDBMS/SQL_기본개념,_Attribute_Type,_Key_Constraints)
[SQL query](/Area/Programming/Skills/Database/RDBMS/SQL_query)
[SQL에서 NULL의 의미](/Area/Programming/Skills/Database/RDBMS/SQL에서_NULL의_의미)
[SQL과 JOIN](/Area/Programming/Skills/Database/RDBMS/SQL과_JOIN)
[SQL과 정렬 및 통계](/Area/Programming/Skills/Database/RDBMS/SQL과_정렬_및_통계)
[데이터베이스 트랜잭션, ACID](/Area/Programming/Skills/Database/RDBMS/데이터베이스_트랜잭션,_ACID)
[데이터베이스 설계-정규화](/Area/Programming/Skills/Database/RDBMS/데이터베이스_설계-정규화)
[데이터베이스 설계-DB index](/Area/Programming/Skills/Database/RDBMS/데이터베이스_설계-DB_index)
[데이터베이스 설계-파티셔닝, 샤딩, 레플리케이션](/Area/Programming/Skills/Database/RDBMS/데이터베이스_설계-파티셔닝,_샤딩,_레플리케이션)
[데이터베이스 성능 최적화-서버 리소스 활용](/Area/Programming/Skills/Database/RDBMS/데이터베이스_성능_최적화-서버_리소스_활용)
[데이터베이스 성능 최적화-DB connection pool](/Area/Programming/Skills/Database/RDBMS/데이터베이스_성능_최적화-DB_connection_pool)
[RDB vs NoSQL](/Area/Programming/Skills/Database/RDBMS/RDB_vs_NoSQL)


[-] 
- SQL concurrency control, 트랜잭션의 Isolation 보장하기
  : serial schedule만 사용한다면 데이터 일관성을 해칠일은 없겠지만 성능 이슈가 있다.
    big data를 다룬다면 성능을 위해 none-serial schedule을 실행할 수 있도록 반드시 익혀두자.
    > [!rf]
    > [개념 강의](https://www.youtube.com/watch?v=DwRN24nWbEc&list=PLcXyemr8ZeoREWGhhZi5FZs6cvymjIBVe&index=15)


### ETC

[snake_case to camelCase](/Area/Programming/Skills/Database/ETC/snake_case_to_camelCase)


## Network

### proxy server


[(ssh 등)원격지의 프로세스의 네트워크 요청을 로컬 환경의 네트워크 인터페이스로 날리기](/Area/Programming/Skills/Network/proxy_server/원격지의_프로세스의_네트워크_요청을_로컬_환경의_네트워크_인터페이스로_날리기)
- nginx:
  웹서버의 기능을 하는 만큼 들어오는 요청들을 적제 적소의 endpoint로 뿌려줄 수 있다.
