github actions은 자동화 툴이다. github과 연관된 어떤 작업에 트리거를 설정해서 원하는 명령을 실행할 수 있다.

명령을 실행한다면 역시 시스템이 필요하고, 이것을 기본적으로는 github에서 대여해준다. 신기한게 3대 운영체제 모두 대여해준다.

다만 private repository에는 다소 제한이 있어서, 본격적인 상업용으로는 사용이 어렵다. 물론 특별한 일이
아니라면 private의 제한된 허용시간으로도 충분할 것이다.

허나 나는 내 멋대로 하는게 좋으니까 self-hosted runner를 사용하자. 여기서 runner는 위에서 표현한 system이다.
github actions의 명세를 수행할 시스템을 뜻한다.

## self-hosted runner 사용법

원하는 github repository의 setting에서 설정할 수 있다. self-hosted runner라고 큰 버튼이 있는데, 눌러보면
복붙만으로 모든 설정을 끝내는 명령어들을 안내하고있다.

## 주의할 점

복수의 repository를 하나의 runner에서 다룰 수 없다. 도대체 왜 못하게 해놨는지 모르겠다. 다만 organization
단위로 runner를 공유할 수는 있다.

최근들어 org로 repository를 구분하기 시작했는데, 그래서 썩 나쁘지는 않다. org마다 하나의 runner서버를 둔다고 생각하면 되겠다.
