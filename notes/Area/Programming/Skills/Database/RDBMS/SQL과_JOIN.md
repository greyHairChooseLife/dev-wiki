# SQL과 JOIN


## JOIN이란?

- _두개 이상의 table들에 있는 데이터를 한 번에 조회하고 result table에 담는 것_
- _여러 종류의 `JOIN`이 있음_


## JOIN의 변화

### (과거) implicit join

- `FROM`절에는 table들만 나열하고, `WHERE`절에 join condition을 명시하는 방식
- **단점**: `where`절에 selection conditions와 join condition이 같이 있어서 복잡하고 실수하기 쉬움

### (개선) explicit join

- `FROM`절에 `JOIN` 키워드와 함께 joined table들을 명시하는 방식
- `JOIN <joined table> ON <join conditions>`


## "Inner" Join vs "Outer" Join and ...

### Inner Join

- join condition을 **만족하는(TRUE인 경우) tuple들만** result table에 포함
  즉, NULL 값(논리 연산 결과가 UNKNOWN)을 가지는 tuple은 result table에 포함 안됨

- 사용법: 그냥 `JOIN`하면 됨
  (join은 기본적으로 inner join이다. 생략 가능할 뿐이다.)

### Outer Join

- join condition을 **만족하지 않는 tuple들도** result table에 포함
- 이때, 없는 값은 전부 NULL(join condition을 만족하지 않는다면 양 쪽 테이블 중 한쪾에만 row가 존재할 것.)

- 3가지 종류 별 사용법:

  | 종류 별 사용                       | 결과(result table)                                    | 비고              |
  |------------------------------------|-------------------------------------------------------|-------------------|
  | table_L LEFT (OUTER) JOIN table_R  | table_L에서 join condition을 만족하지 "못한"것도 포함 |                   |
  | table_L RIGHT (OUTER) JOIN table_R | table_R에서 ...                                       |                   |
  | table_L FULL (OUTER) JOIN table_R  | table_L, table_R에서 ...                              | mysql에 지원 안함 |

### Equi Join

- join condition으로 = (equality comparator)를 사용하는 join
  (inner join에만 한정하기도 하고, outer join까지 포함하기도 하고... 경우에 따라 다르다.)

- 사용법: join condition이 `table_L.atrr = table_R.atrr`일 때 `USING (attr)`라고 바꿔 쓸 수 있음
  (attribute name이 일치해야 함)

  이러면 joined result table에서 중복된 2개의 attribute대신 단일한 attribute을 얻음

### Natural Join

- 두 table에서 **같은 이름을 가진** 모든 attribute pair에 대해서 equi join을 수행
  (이름이 같지만 의미가 다른 두 attributes가 있다면 문제가 될 것)
- join condition 필요 없음

- 사용법:  inner, outter 모두 사용 가능
  `FROM table_L NATURAL <some> JOIN table_R`

### Cross Join

- 두 table의 tuple pair로 만ㄷ르 수 있는 모든 조합(= Cartesian product)을 result table로 반환
- join condition 없음

- 사용법:

  | 종류                | query                           |
  |---------------------|---------------------------------|
  | implicit cross join | FROM table_1, table_2           |
  | explicit cross join | FROM table_1 CROSS join table_2 |

  * mysql에서는 좀 달리 동작할 수 있으니, 반드시 검증 후 사용

### Self Join

- table이 자기 자신에게 join
