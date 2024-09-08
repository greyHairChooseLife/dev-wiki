# 󰏢 anki-diary


## 1. 개념 [-]

[-] 아래는 핵심 기능이나 특징이긴 한데, 재작성 필요해 보인다.

old-card vs rewrite-card => selection

1. best-card와 rewrite-card를 비교하여 작성 된 것 중 best를 선택한다.
2. 좌우가 비교 되도록 하는데, 작성시간, 글자수, 작성일 등 기본적인 정보도 같이 띄워서 비교 해 준다.
3. 선택은 2분 이내로, 버리기 더 아까운 것을 선택하여 best-card로 남긴다.

- 동일한 날에 최대 2개까지 job이 쌓이고, new-write도 이 최대치에 포함이다.
- 작성을 완료하고 다음 frequency를 선택 하는 순간 다음에 작성 할 날이 정해져야하는데(유저에게 노출은 안돼도), 이때 최대치에 걸리지 않도록 해야한다.

## 2. 핵심 요소

- new-write card

  - 격일로 작성한다.
  - 5분짜리 유투브라도 보고 쓰던가. 존경하는 사람이 남긴 글이라도 보고 쓰던가. 영화라도 쓰던가. 시라도 보던가.
  - 최대 300자/15분 이내로 제한.


- re-write card

  - writing rules
    - old-card의 작성 시간과 비교하여, 그 이하가 되도록 제한한다. 최소 시간은 3분.
    - old-card의 제목으로 작성이 완료되었으면, 새로운 글의 제목도 다시 적는다. chatGPT한테도 물어보자. api연결을 할 수도?

  - frequency rules
    - 작성 후 '옵션'을 선택하면 '값'의 범위 내에서 재등장한다.
      | 옵션 | 값(일) |
      |------|--------|
      | 빨리 | 4~8    |
      | 중간 | 7~15   |
      | 나중 | 20~30  |
      | 멀리 | 40~50  |
      | 랜덤 | 25~50  |


## 3. 개발

- card type

  - card_type : 'life' | 'dev-study'
  - created_date
  - time_consuming
  - accumulated_count
  - subject
  - content
  - frequency : 'soon' | 'later' | 'random'
    |        | incomming after (day) |
    |--------|:---------------------:|
    | soon   |         4 ~ 8         |
    | later  |        7 ~ 15         |
    | random |        1 ~ 14         |

## 참고

[디자인 참고](https://www.framer.com/templates/daedalus/)
[디자인 참고](https://www.framer.com/templates/bjgroup/)
[디자인 참고](https://www.framer.com/templates/justtype/)
[디자인 참고](https://www.framer.com/templates/photography-website/)
[디자인 참고](https://www.framer.com/templates/plain/)
[디자인 참고](https://www.framer.com/templates/luca/)
[디자인 참고](https://www.framer.com/templates/appleseed/)
[디자인 참고](https://www.framer.com/templates/source/)
[디자인 참고](https://www.framer.com/templates/uno/)
