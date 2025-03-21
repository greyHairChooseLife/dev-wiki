# SQL에서 NULL의 의미



## NULL의 의미

| 구분           | 의미                      | 예시                         |
|----------------|---------------------------|------------------------------|
| unknown        | 있지만 아직 모름          | 아직 업데이트 안됨           |
| unavailable    | 안알려줄거임              | 사용자가 공개 거절 함        |
| not applicable | 존재하지 않음(할 수 없음) | 미성년자는 주민등록증이 없음 |


## 조건절에 NULL 사용하기

_`IS` 또는 `NOT IS` 를 사용해야 한다._

| 명령                           | 평가     | 결과                              |
|--------------------------------|----------|-----------------------------------|
| SELECT ... WHERE value = NULL  | ass hole | 아무것도 없다고 나옴              |
| SELECT ... WHERE value IS NULL | good job | 해당 attribute이 NULL인 것을 찾음 |


## NULL과 Three-Valued Logic

`WHERE`절로 조건을 따져보는 atrribute에 NULL이 있는 경우 어떻게 판단하나? TRUE or FALSE?
`UNKNOWN(unknown)`이 된다. TRUE or FALSE 모두 가능하기 때문이다.

즉, Three-Valued Logic이란 **비교연산의 결과**가 TRUE or FLASE or UNKNOWN **세가지 중 하나**라는 것

| 비교 연산 예시      | 결과        |
| :----------------:  | :---------: |
| 1 = 1               | TRUE        |
| 1 != 1              | FALSE       |
| 1 = NULL            | UNKNOWN     |
| 1 != NULL           | UNKNOWN     |
| NULL = NULL         | UNKNOWN     |


## WHERE절의 conditonis

- _`WEHRE`절의 conditions 결과가 TRUE인 tuple(s)만 선택된다.
  즉, 결과가 FALSE 또는 UNKNOWN인 것은 선택 안된다._

- _`WHERE`절의 conditions는 `AND`, `OR`, `NOT`으로 논리 연산 된다._

  1. `AND`: 모든 조건이 TRUE여야 TRUE
      - UNKNOWN이 하나라도 있으면 UNKNOWN(FALSE일 가능성 있으니까)
      - 단, FALSE가 하나라도 있으면 무조건 FALSE

  2. `OR`: 조건 하나라도 TRUE라면 TRUE
      - UNKNOWN 하나라도 있으면 FALSE는 안나옴(TRUE일 가능성 있으니까)
      - 단, TRUE 하나라도 있으면 무조건 TRUE

  3. `NOT`: 논리 결과를 역으로 전환
      - NOT TRUE => FALSE
      - NOT FALSE => TRUE
      - NOT UNKNOWN => UNKNOWN


- ex)

  | NOT IN 예제           | (논리 연산) 결과    | 쿼리 결과                                  |
  | --------------------  | :-----------------: | :----------------------------------------: |
  | 3 NOT IN (1, 2, 4)    | TRUE                | 존재                                       |
  | 3 NOT IN (1, 2, 3)    | FALSE               | 없음                                       |
  | 3 NOT IN (1, 3, NULL) | FALSE               | 없음                                       |
  | 3 NOT IN (1, 2, NULL) | UNKNOWN             | 없음(일부 만족하더라도 아예 없는걸로 나옴) |

  즉, NULL과의 비교가 하나라도 껴있으면 다른 정상적인(TRUE OR FALSE를 파악할 수 있는) 비교값들도 다
  죽어버리는 결과가 나온다.
