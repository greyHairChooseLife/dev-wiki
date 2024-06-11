## three-schema architecture

    _아래 아키텍쳐를 잘 구성할수록 장기적으로 비용효율적이다. 급할수록 돌아가자._

    - 외부 스키마(aka. user view)
      : 사용자 관점에서 데이터베이스, 각 사용자가 접근할 수 있는 부분적 데이터베이스를 표현

    - 개념 스키마
      : 전체 데이터베이스의 논리적 구조를 표현, 즉 내부 스키마를 어느정도 추상화하여 표현해서 스키마를
      변경할 때마다 외부 스키마에 영향을 주지 않도록 완충

    - 내부 스키마
      : 데이터베이스의 물리적 저장 구조를 표현

## What does the 'relational' mean?

    _수학 개념에서 따온 것이다. 복수의 집합들 사이에 원소가 조합 될 수 있는 모든 경우의 수를 cartesian이라
    하는데, 수학적으로 '관계'란 이 cartesian의 부분집합을 뜻한다.

    이때 각 집합에 대응되는 개념은 domain이고, SQL에서 table이라 불리는 것의 column을 뜻하게 된다. 그러나
    개념적으로 정확히 구분하면 domain은 원소(데이터)들의 group name이고, 이 도메인들이 모여서 하나의
    테이블을 구성(즉, relation이 발생) 할 때는 table(relation schema) 내에서 SQL의 attribute가 되는 것이다.

    수학적으로 '관계'라는것이 모든 경우의 수의 부분집합이라 하였는데, 이 경우의 수 들은 tuple이라고도 할 수
    있다. 이러한 '경우'들 하나 하나가 relation schema의 rows가 되는 것이다.

    참고로, relation schema는 attributes와 관련된 contraints도 포함된 개념이다._


## relational database

    - relation schemas set + integrity constraints set

## relation의 특징들

    - 중복된 튜플이 없다. (Relation is **set** of tuples.)
    - tuple을 식별하기 위해 attribute의 부분집합을 key로 사용한다.
    - atomic
