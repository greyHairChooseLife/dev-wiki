## ORDER BY

    - 조회 결과를 특정 attribute(s) 기준으로 **정렬**

    | 표기 | 의미     |         |
    |------|----------|---------|
    | ASC  | 오름차순 | default |
    | DESC | 내림차순 |         |

    - 사용법:
      `SELECT * FROM employee ORDER BY attr_1 ASC, attr_2 DESC;`


## aggregate function

    - 여러 tuple들의 정보를 요약해 **하나의 값으로 추출**하는 함수
    - 대표적으로 `COUNT`, `SUM`, `MAX`, `MIN`, `AVG`
    - **NULL 값들은 제외**하고 요약 값을 추출

    - 사용법:
      `AVG(salary)`, `MAX(age)`

      ex) `SELECT COUNT(*) AS empl_count, MAX(salary) FROM employee;` tuple들의 수와 최대 연봉을 추출


    ### GROUP BY

        - aggregate function을 적용하는(찾는)데, **특정 attribute(s)기준으로 그룹을 나눠서 계산**하고 싶을 때
        - 그룹의 기준이 되는 grouping attribute(s)에 NULL 값이 있는 경우는 그것들끼리 묶여서 계산됨
        - 복수의 grouping attributes를 사용하면, 모든 조합을 각각 group으로 여김

        - 사용법:
          `WHERE`를 쓰거나 조건없이 모든 tuple들을 대상으로 aggregate function을 수행하는 대신,

          ex) `GROUP BY <table.attribute>`

    ### HAVING

        - GROUP BY로 그룹별 결과를 낸 result table에서 또다시 조건을 걸어 **필터링**

        - 사용법:
          `GROUP BY` 뒤에 붙여서 사용

          ex) `GROUP BY <table.attribute> HAVING MAX(salary) >= 300만원`


