## Keywords( vs relational model)

    | relational model | SQL    |
    |------------------|--------|
    | relation         | table  |
    | attribute        | column |
    | tuple            | row    |
    | domain           | domain |


## 특이사항

    - multiset of tuples를 허용한다.
        - table이 항상 PRIMARY KEY를 가져야만 하는 것은 아니다.


## WorkFlow

    1. 데이터베이스 스키마 설계

        - 테이블의 종류, 종류별 column구성
        - 각 테이블 사이에 관계 표시
            - (primary key - foreign key 설정 및 연결)
        - N:M 관계를 표현하기 위해 사용하는 테이블 구성
            - 교차 테이블 || 조인 테이블 || junction table || bridge table
            - 다대다 관계에서는 primary key도 두 atrribute의 조합으로 구성(그래야 unique함)


## Attribute Type

    - 숫자형:

        | 종류        | 설명                                                | mysql(SQL 표준은 아님) |
        |-------------|-----------------------------------------------------|------------------------|
        | 정수        |                                                     | INT, BIGINT 등         |
        | 부동 소수점 | 실수(real number)를 저장할 때 사용, 부정확함        | FLOAT, DOUBLE          |
        | 고정 소수점 | 실수를 정확하게 저장, 몇번째 자리까지 표현할지 정함 | DECIMAL, NUMERIC       |

    - 문자열:

        | 종류               | 설명                                                   | mysql(SQL 표준은 아님)               | 비고             |
        |--------------------|--------------------------------------------------------|--------------------------------------|------------------|
        | 고정 크기 문자열   | 넘치면 에러, 짧으면 공백이라도 채워서 저장             | CHAR(n)                              | 0 <= n <= 255    |
        | 가변 크기 문자열   | 최대값만 적어주면 넣은 사이즈대로 저장                 | VARCHAR(n)                           | 0 <= n <= 65,535 |
        | 사이즈가 큰 문자열 | 사이즈 큰거 저장할 때 사용, 그냥 TEXT는 VARCHAR와 동일 | TINYTEXT, TEXT, MEDIUMTEXT, LONGTEXT |                  |

    - 날짜와 시간:

        | 종류        | 설명                                                   | mysql(SQL 표준은 아님)        | 비고                  |
        |-------------|--------------------------------------------------------|-------------------------------|-----------------------|
        | 날짜        | 년, 월, 일                                             | DATE                          | YYYY-MM-DD            |
        | 시간        | 시, 분, 초, 경과된 시간을 저장하기도 한다.             | TIME                          | hh:mm:ss or hhh:mm:ss |
        | 날짜 + 시간 |                                                        | DATETIME, TIMESTAMP(utc 기준) | YYYY-MM-DD hh:mm:ss   |

    - 그 외:

        | 종류        | 설명                                 | mysql(SQL 표준은 아님)       | 비고                          |
        |-------------|--------------------------------------|------------------------------|-------------------------------|
        | byte-string | (문자열이 아니라) byte string을 저장 | BINARY, VARBINARY, BLOB type |                               |
        | boolean     | mysql에는 없다.                      | TINYINT로 대체해서 사용      | 0 or 1                        |
        | 위치 정보   |                                      | GEOMETRY 등등                |                               |
        | JSON        | json 형태 그대로 저장                | JSON                         | {"name": "messi", "age": 38 } |


## KEY CONSTRAINT

    - `DEFAULT`
      : 기본값 설정
    - `PRIMARY KEY`
      : row간에 중복 불가, null도 불가
    - `UNIQUE KEY`
      : UNIQUE로 지정된 attribute는 중복 값 불가 (RDBMS 종류마다 NULL은 허용할수도 있음)
    - `NOT NULL KEY`
      : NULL 불가를 명시적으로 표현, UNIQUE KEY와 자주 함께 사용
    - `CHECK`
      : attribute 하나 또는그 이상으로 구성해서 value의 범위를 제한
    - `FOREIGN KEY` aka. `REFERENTIAL INTEGRITY`
      : 다른 테이블의 PRIMARY KEY를 참조하는 attribute
        - **핵심적인 제약사항으로 사용 무결성을 지키기 위해 여러 callback option을 지정할 수 있다.**
        - ex)
          ```sql
          CREATE TABLE Employee (
              ...
              department_id INT,
              FOREINGN KEY (department_id)      # foreign key가 될 attribute를 지정
                  references Department(id)     # 참조할 테이블과 attribute를 지정
                  on delete <reference option>  # 참조값이 삭제될 때의 옵션
                  on update <reference option>  # 참조값이 변경될 때의 옵션
          );
          ```
          | reference option           | 설명                                                                                                   |
          | :------------------------: | ------------------------------------------------------------------------------------------------------ |
          | CASCADE                    | 참조값의 삭제/변경을 그대로 반영                                                                       |
          | SET NULL                   | 참조값의 삭제/변경 시 NULL로 변경                                                                      |
          | RESTRICT                   | 참조값의 삭제/변경 금지                                                                                |
          | NO ACTION (mysql 지원 X)   | transaction이 진행 중엔 참조값의 삭제/변경을 허용하다가, 완료시 constraints 발생하면 transaction 거부  |
          | SET DEFAULT (mysql 지원 X) | 참조값의 삭제/변경 시 attribute의 DEFAULT로 변경                                                       |


    * contraints에 이름을 명시할 수 있다.

      ```sql
      CREATE TABLE Employee (
          ...
          age INT CONTRAINTS age_over_20 CHECK (age > 20)
          ...
      );
      ```
      constraints 위반시 에러 메시지에 'age_over_20'이라는 이름이 나온다.
