# 󰏢 design


## 가벼운게 최고

(나의 경우)css프레임워크 도입 목적이 비용을 줄이는 것인데, 배우기 어렵고 손이 많이 가면 아무 의미가 없다.

또 프레임워크에 갇혀서 _때때로 구체적이고 명확해지는_ 고객의 요구사항을 수용하기 어려워서는 안된다.

적은 클래스네임과 커스텀 유연성이 중요하다. 이를 강조하는 프레임워크중 쓸만한 것을 아래처럼 정해봤다.
죽이되든 밥이되든 사용하면서, 커스텀 숙련도를 천천히 높여가자.

> [!rf]
>
> - 가벼운 css 프레임워크
>   [pico](https://github.com/picocss/pico)
>   [spectre](https://github.com/picturepan2/spectre)
>   [4 Lightweight CSS Frameworks](https://cs310.hashnode.dev/starting-a-new-side-project-here-are-4-lightweight-css-frameworks-to-get-up-and-running)
>
> [css 따위 안쓰는 사나이클럽, 코딩애플](https://www.youtube.com/watch?v=T3lEM26r_2s)


## why not Tailwind? [-]

난 한번도 안써봤다. 근데 딱봐도 뭔 css를 덕지덕지 붙여놨더라.

1. css파일을 따로 만들어서 관리하는 비용이 대폭 줄어들고,
2. html 태그만 봐도 디자인 유추

되는게 장점이란다.

따로 css파일을 관리하지 않는다는 점은 양날의 칼인데, css프레임워크를 쓰냐 마냐의 차이지 테일윈드의 장점은
아니다.

그리고 html만 봐도 유추할수 있다는건... 그냥 프리뷰로 띄워놓고 보면 안되나? 나야말로 cli환경 참 좋아하는데,
그림은 그림으로 봐야지.

뭣보다 그 고유명사들을 외우고 있어야 하는거 자체가 싫다.


[-]
[tailwind 4.0이 매우 좋아졌다](https://www.youtube.com/watch?v=2Q7x3LNZPJs)


## 애니메이션

웬만한 에니매이션은 [여기서](https://animista.net/) 쉽게 구현할 수 있다.

> [!rf]
>
> [위 사이트 소개 영상](https://www.youtube.com/watch?v=OWaHfpHxgb8)


## 웹 개발에 자주쓰이는 해상도

- desktop _(1920 x 1080)_

  - 일명 FHD(Full High Definition)라고 불리는 해상도
  - 24인치 데스크탑, 보편적인 모니터 크기의 해상도
  - XD 나 figma 에서 작업시 뷰포트는 1053px
  - 브라우저의 상단 부분의 URL 과, 북마크 등의 높이값을 빼고 작업을 한다. viewport는 약, 1053px 정도로 설정
  - **다만,** 배경 등 레이아웃 요소들을 1920기준으로 하되, 본문 등 실제 내용은 1000px정도로 작업


- laptop _(1536 x 864)_

  - FHD 노트북의 해상도
  - 1920 x 1080에 125% 배율을 적용한 해상도
  - 노트북 폰트 사이즈 기본 세팅이 125%로 되어 있는경우가 대부분
  - 브라우저의 상단 부분의 URL 과, 북마크 등의 높이값을 빼면 viewport는 약, 845px 정도로 설정


- tablet _(1024 x 768)_

  - 스마트폰 기기의 해상도는 너무 다양하기 때문에 모든 기준을 맞추지는 못함
  - 일반적 표현되는 해상도 기준에 맞춰 "media" 테그를 활용하여 반응형 웹으로 제작


- mobile _(375 x 667)_

  - 375 x 667 해상도는 iPhone 6, 7, 8, X 등의 기본 해상도


> [!ye] 해상도에 맞게 미디어 쿼리 작성 방법 예시
>
> _1920px은 기본값으로 별도의 미디어 쿼리 설정 없이 사용_
>
> ```css
> p{
>     font-size: 1.2rem;
> }
> @media (max-width: 1536px) {
>     p{
>         font-size:1rem;
>     }
> }
> /* 1280 ~ 1536 해상도 부터 body의 font-size가 1.2rem  */
> @media (max-width: 1280px) {
>     p{
>         font-size:0.8rem;
>     }
> }
> @media (max-width: 1023px) {
> }
> @media (max-width: 766px) {
> }
> ```



## ETC

[CSS 게임: Css Diner](https://flukeout.github.io/)
