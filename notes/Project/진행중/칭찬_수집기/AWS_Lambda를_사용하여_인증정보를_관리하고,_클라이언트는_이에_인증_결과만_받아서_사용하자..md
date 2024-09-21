# 󰏢 AWS Lambda를 사용하여 인증정보를 관리하고, 클라이언트는 이에 인증 결과만 받아서 사용하자.


Lambda 개념만 알지 사용은 안해봤다. 자세히 기록해보자.

## how to? [-]

1. AWS Lambda 함수 생성

   - 함수명: compliment-collector
   - 런타임: Node.js 20.x

2. Lambda에 google cloud 서비스계정 키 업로드:
   "구성-환경변수"에 업로드

3. 코드 작성

4. 코드 업로드

   _(cli로 뭔가 편리한 방법 있을텐데...)_

   - 설치해야 하는 패키지라도 있다면 node_modules/ 따위도 모두 업로드 해야함
   - 프로젝트를 통째로 zip 파일로 만들어서 업로드
     `zip -r function.zip .`

   - 이 경우 시작 포인트는 package.json에 정의하는게 아니고 핸들러를 정의해야한다. 기본적으로는 index.handler인데, `<파일명>.<파일 내 함수명>`을 뜻한다.
     즉, index.js 파일에 `(commonJS의 경우)exports.handler = ...` 또는 `(ESmodule의 경우)export const handler = ...`에 할당된 함수가 시작 포인트인 것이다.

     물론 이를 변경할 수도 있다.

5. (테스트에 성공하면) API Gateway를 통해 Lambda 함수를 호출할 수 있도록 설정

   - 트리거 추가 -> API Gateway -> HTTP API로 설정

   > [!re] api gateway를 생성할 때 cors를 임의 설정하지 않더라도, 보안상 기본 설정되어있다. 그러니 허용할 Access-Control-Allow-Origin을 설정해주자.
   > - 테스트: *
   > - 배포  : 실제 url

6. 클라이언트에서 api gateway를 통해 정상 호출 확인, 배포시 보안 개선

   > [!re] lambda 함수의 timeout은 3초 기본이었는데, 부족할 대가 있어서 10초로 늘려줬다.


   > [!td]2024-09-21
   > [-] AWS api gateway cors 설정: `'*' -> <실제-url>`
   >  󱞪 이게 https://greyhairchooselife.github.io/compliment-collector/ 넣고 해봐도 안된다.


