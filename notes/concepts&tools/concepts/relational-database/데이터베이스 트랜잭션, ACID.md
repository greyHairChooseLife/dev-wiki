## 트랜젝션(Transaction)

    - 단일한 논리적 작업단위
    - 여러 SQL statements를 단일작업으로 묶어서 실행
    - 마지막 하나라도 실패하면 모두 실패(모두 성공해야 실제로 DB에 반영)

    - 사용법:

      ```sql
      -- account table
      -- | id | balance |
      -- | -- | ------- |
      -- | J  | 100     |
      -- | H  | 500     |

      mysql> START TRANSACTION; -- autocommit 임시 해제
      mysql> UPDATE account SET balance = balance - 100 WHERE id = 'J';
      mysql> UPDATE account SET balance = balance + 100 WHERE id = 'H';
      mysql> COMMIT; -- statement(s) 영구반영
      -- mysql> ROLLBACK; -- statement(s) 모두 취소
      ```

    * autocommit

        _원래는 모든 SQL statements가 commit을 해야 반영된다. 그런데 autocommit이란게 활성화되어있어서 자동으로
        커밋 되는것.

        그런데 `START TRANSACTION;`을 통해 잠시 autocommit을 해제하는 것이다._

## ACID

    - 트랜잭션을 작성시 지켜야하는 속성

        1. Atomicity

           _All or Nothing (더이상 쪼개질 수 없다.)_

        2. Consistency

           _데이터 일관성을 위해 DB에는 key constraints 등이 준비되어있다. 그러나 동시에 application
           관점에서도 transaction이 consistent하게 동작하는지 점검 해야한다._

        3. Isolation

           _여러 transaction들이 동시에 실행될 때 섞이지 않게 해야한다.

           예를들어 `UPDATE SET balance = balance + 100원`이 어느 하나가 commit되기 전에 실행된다 치면,
           최종적으로 100원만 추가 될 것이다.

           DBMS는 이를 방지하기 위해 여러 종류의 isolation level을 제공하며, 개발자가 이 정도를 선택할 수
           있다. **높은 isolation level은** 데이터 일관성을 유지하는데 도움이 되지만, 그만큼 동시실행이 불가하여
           성능이 저하 되는 **양날의 검**이다._

        4. 11.Durability (영존성)

           _기본 설정이 영구보존성을 보장한다. DBMS가 책임지는 영역_
