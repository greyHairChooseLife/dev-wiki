## index가 중요한 이유

    _특정 **조건을 만족**하는 tuple(s)를 빠르게 읽기/정렬/그룹핑 하기위해 사용_


## index 거는 법

    - 이미 생성된 테이블에

        ```sql
        CREATE INDEX <index_name> ON <table_name> (<column_name>);
        -- 또는
        CREATE UNIQUE INDEX <index_name> ON <table_name> (<column_name>);
        ```
          * `UNIQUE INDEX`는 index를 걸려는 column(s)가 `(candidate) key`인 경우 사용

    - 테이블 생성시

        ```sql
        CREATE TABLE <table_name> (
            <column_name> <data_type> <constraint>,
            ...,
            INDEX <index_name> (<column_name>)
            -- 또는
            UNIQUE INDEX <index_name> (<column_name(s)>)
        );
        ```
        * column 여러개를 묶어서 index를 지정하는걸 multi-column index 또는 composite index라고 한다.
        * primary key는 index가 자동 생성된다.

    - 테이블의 인덱스 확인

        ```sql
        SHOW INDEX FROM <table_name>;
        ```


## index 동작 방식

    _B-tree 방식으로 index를 조회한다. 쉽게말해 1~100 중에 숫자 하나(정답)를 떠올리고, 상대는 가장 적은
    횟수의 시도로 정답을 맞추는 게임과 같다._


## index 사용 시 참고 사항

    1. _인덱스는 임의로 걸 수 있고, 내 query의 조건절이 여러가지 index를 활용할 수 있다.
       뭐가 쓰이는지 어떻게 알 수 있나?_

       => select query 앞에 `EXPLAIN`을 붙여서 실행하면 된다. 'possible keys' 사용가능한 index, 'key'에
       실제로 사용한 index가 출력된다.

    2. _여러 index 중 무엇이 가장 효율적일까?_

       => optimizer가 알아서 해준다.

    3. _내 맘대로 index를 고르고싶으면?_

       =>
          | 용도                          | command                                 |
          |-------------------------------|-----------------------------------------|
          | optimizer야, 가급적 이거 써라 | SELECT ... USE INDEX (<index_name>);    |
          | 무조건 이거 써라              | SELECT ... FORCE INDEX (<index_name>);  |
          | 이것만은 쓰지 마              | SELECT ... IGNORE INDEX (<index_name>); |


    4. _이렇게 좋은데, 많이많이 만들수록 좋은가?_

       => 양날의 검이다. 검색 빠르지만,

            - write 할 때마다 index를 재정렬
            - index를 위한 저장공간을 차지함
