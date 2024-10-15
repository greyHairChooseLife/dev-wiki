# 󰏢 browser


## brave browser

- 선택 이유:
  - 광고 차단 내장형이다.
  - 오픈 소스다.
  - 빠르다. 체감 속도도 빠르다.

### dark mode

- dark mode

  아직 미완성이 기능인데, 구글 캘린더, 지메일을 다크모드로 간단히 만들어준다.

  - how to?

    1. 브라우저에 다음의 주소로 들어간다. `brave://flags/#brave-ntp-branded-wallpaper-demo`
    2. 'Auto Dark Mode for Web Contents'옵션을 'Enabled with selective inversion of non-image contents'로 바꾼다.

  - 웹 개발할 때 정확한 색을 알아보기 위해 꺼줘야한다.
  - 이거하니까 surfingkeys의 hints 색상에도 영향이 있다. 기존 색이 inverse되어서 그런듯.


### surfingkeys extension


#### 내장 개념

- **Omnibar**: telescope plugin처럼 떠있는 보조창. search, bookmark 따위에 나타난다.


#### 필수 단축키

- Normal Mode
  | 조합 키             | 실행                             | 비고                                     |
  |---------------------|----------------------------------|------------------------------------------|
  | n: `<A-s>`          | 현재 사이트에서 끄기             | toggling                                 |
  | n: `o[gyr]-<space>` | 사전정의된 사이트에서 검색       | g: 구글, y: 유투브, r: reddit            |
  | n: `b`              | 새 탭에서 bookmark 열기          |                                          |
  | n: `ab`             | add bookmark                     |                                          |
  | n: `;db`            | delete bookmark                  | Omnibar에서 `<ctlr-d>`로 삭제            |
  | n: `W<CR>`          | 현재 탭을 새 윈도우에            | 기존의 다른 윈도우로 옮길 수도 있다.     |
  | n: `;di`            | 선택한 이미지 다운로             | 기존의 다른 윈도우로 옮길 수도 있다.     |
  | n: `;fs`            | show scrollable area to focus    | `cs, cS` 외에도 이런 선택지가 있다.      |

- Visual Mode
  | 조합 키           | 실행                          | 비고                          |
  |-------------------|-------------------------------|-------------------------------|
  | v: `s[g/y/pe/pk]` | search selected text          | 구글/유튜브/파파고(영한/한영) |
  | v: `gr`           | read selected text with voice | brave는 안된다.               |


- Omnibar
  | 조합 키             | 실행                             | 비고                                     |
  |---------------------|----------------------------------|------------------------------------------|
  | <C-k/j>             | 위/아래 요소 선택                |                                          |


#### 별로 안쓰는 단축키

| 조합 키             | 실행                             | 비고                                     |
|---------------------|----------------------------------|------------------------------------------|
| n: `;cp`            | 현재 프록시 정보 클립보드에 복사 |                                          |
| n: `;pp`            | 클립보드 내용을 브라우저에 표현  |                                          |
