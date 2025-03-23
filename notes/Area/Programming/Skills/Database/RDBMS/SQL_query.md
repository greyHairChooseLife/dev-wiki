# SQL query



## SQL 기본명령어


> [!INFO]
>
> mysql 기준이다.


### databases || schemas

- `SHOW databases;`
- `CREATE database;`
- `DROP <database>;`
- `USE <database>;`

### tables

- `SHOW tables;`
- `CREATE TABLE <table> ( ... );`
- `DESC <table>;`
- `ALTER TABLE <table> ADD <column> <type>;`
- `DROP TABLE <table>;`

### rows

- `INSERT INTO <table> VALUES ( ... );`
- `UPDATE <table> SET <column> = <value> WHERE <condition>;`
- `DELETE FROM <table> WHERE <condition>;`
- `SELECT * FROM <table>;`
    - `AS`의 사용
      : table에 alias를 붙여서 query를 명확하게 표현
      : attribute에 alias를 붙여서 result의 column명을 변경
    - `DISTINCT`
      : select의 결과에서 중복된 row를 제거
    - `LIKE`
      : pattern으로 필터링

      | pattern 종류    | 설명                                 |
      |-----------------|--------------------------------------|
      | %               | 0개 이상의 임의의 개수를 가진 문자들 |
      | _ (under score) | 단일한 문자                          |
      | \               | escaper                              |

    * 조건(WHERE)을 포함해서 조회한다면, 조건에 관련된 attributes에 index가 걸려있어야 한다.
      아니라면 성능이 매우 떨어진다.

    - `subquery`
      : query의 조건이 될 값을 또다른 query를 통해 얻을 수 있을 때, main query 안에 괄호로 감싸 넣음
      : 예시)
        ```sql
        mysql> select id, name from employee
            -> where birth_date < (
            ->      select birth_date from employee where id = 14
            ->      );
        ```

    - `IN` or `NOT IN`
      : ex) `value IN (v1, v2, ...)`
        value가 (v1, v2, ...) 중에 어느 하나와 같다면 TRUE
        (v1, v2, ...) 이것은 `subquery`의 결과일 수도 있다.

    - `EXISTS` or `NOT EXISTS`
      : `subquery`의 result가 1개 이상이라면 TRUE

    - `ANY` or `SOME`
      : ex) `value comparison_operator ANY (subquery)`
        value가 subquery의 어느 하나와라도 comparison_operator를 만족하면 TRUE

    - `ALL`
      : ex) `value comparison_operator ALL (subquery)`
        value가 subquery의 모든 rows와 comparison_operator를 만족하면 TRUE
