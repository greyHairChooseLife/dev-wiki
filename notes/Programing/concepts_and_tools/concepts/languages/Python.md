# 󰏢 Python


> [!rf]
>
> [조코딩 2024' 무료강의](https://www.youtube.com/watch?v=ftQZo7XaTOA)


## 특징

- interpreter 언어다.
- C와 궁합이 좋다.
- 하나의 목적을 달성하는 하나의 문법만 존재한다. -> 근데 버전업 되면서 많이 퇴색됨


## 셀프 질문

> [!qt] 언제 깔린지는 모르겠다만 파이썬 인터프리터를 실행하려하니 다양한 패키지가 있다. 이것들은 무엇인가?
>
> ```bash
> python
> python3
> python3.12
> python3.12-config
> python3-config
> python-argcomplete-check-easy-install-script
> python-config
> python-typer
> ```
>
>   󱞪 일반적으로 사용하는 interpreter는 `python`을 실행하면 된다. 기본적으로는 최신 버전이 맵핑되는듯하다.
>     만약 특정 버전이 필요하면 뒤에 특정 버전이 붙은 것을 실행하면 된다.
>     이를 수정하고싶으면 `pyenv`를 활용한다.
>
>   󱞪 python-config는 파이썬을 C언어 등 다른 환경과 연동할 때 필요한 환경정보를 제공한다.
>
>   󱞪 이밖에 이름이 긴 것들은 별도 설치된 패키지를 위한 스크립트 들이다. 설치된 파이썬 패키지를 활용하기 쉽게 도와준다.


## TOC

### 자료형


#### None

- 아무것도 없음을 나타내는 자료형이다.
- `None`은 `False`로 간주된다.
- `is` 연산자로 `None`인지 확인할 수 있다.
  ex) `thisValue is None`

- JS의 `undefined`와 비슷하다.


#### 숫자형: 정수, 실수 등

연산자에는 `+`, `-`, `*`, `/`, `//`, `%`, `**`가 있다.

- `//`: 몫만 얻는다.
- `**`: 제곱을 구한다.


#### 문자형


문자열은 `''`, `""`, `''' '''`, `""" """`로 표현한다.

- `\` 이스케이핑도 가능하다. `\n` 줄바꿈, `\t` 탭 등 특수 이스케이핑도 가능하다.

- 문자열에 `*` 연산자를 사용가능
  ex) `print('1' * 10)` -> `1111111111`

- index: `[n]`

- slice: `[이상:미만]`, `[이상:미만:간격]`

  **실제 값을 변경하는것은 아니고, 새로운 값을 반환한다.**

  ```python
  print('hello'[1:3])  #el
  print('hello'[::2])  #hlo
  ```

  - 어느 칸이 비어있다면 '처음부터', '끝까지' 를 의미한다.
    ```python
    print('hello'[:3])   #hel
    print('hello'[1:])   #ello
    print('hello'[:])    #hello
    ````

  - 음수가 들어가면 거꾸로 센다.
    ```python
    print('hello'[-1])   #o
    print('hello'[-3:])  #llo
    print('hello'[::-1]) #olleh
    ```

- 문자열 포맷팅 formatting

  - 방법 1) `%d, %s` 등을 사용
    ```python
    print('I have %d apples' % 3)  #I have 3 apples
    print('I have %s apples' % 'three')  #I have three apples
    print('I have %s apples and %d bananas' % ('three', 5))  #I have three apples and 5 bananas
    ```

    - `%s`는 **모든 타입**을 받을 수 있다.
    - '%' 문자 자체를 넣고싶을 땐 `%%`를 사용한다.

  - 방법 2) `format()` 함수를 사용
    ```python
    print('I have {} apples'.format(3))  #I have 3 apples
    print('I have {} apples and {} bananas'.format('three', 5))  #I have three apples and 5 bananas
    print('I have {1}, {2}, {0}.'.format('car', 5, 10))  #I have 5, 10, car.
    ```

  - 방법 3) `f` 사용, **(거의 이걸 쓴다.)**
    ```python
    apple = 3
    banana = 5
    print(f'I have {apple} apples and {banana + 1} bananas')   #I have 3 apples and 6 bananas
    ```


- `find, count, join, upper, lower, (l/r)strip, replace, split` 등의 메소드가 있다.


#### list

- 다양한 타입을 담을 수 있다. 좀 느리긴 하지만
- 문자열과 같은 방식으로 인덱싱, 슬라이싱 가능

- `+`, `*` 연산자 사용 가능

  ```python
  a = [1, 2, 3]
  b = [4, 5, 6]
  print(a + b)  #[1, 2, 3, 4, 5, 6]
  print(a * 3)  #[1, 2, 3, 1, 2, 3, 1, 2, 3]
  ```

- `del, append, sort, reverse, index, insert, remove, pop, count, extend` 등의 메소드가 있다.


#### tuple

- 바꿀 수 없는 리스트. 자물쇠가 채워져 있다.
  따라서 `sort, pop, insert` 등의 메소드는 사용할 수 없다.

- `( )`로 묶어서 표현한다. 만약 1개의 원소만 있다면 `(1,)`처럼 쉼표를 꼭 붙여야한다.
  괄호를 생략해도 문법상으로는 문제가 없지만 가독성을 위해 써주자.


  #### dictionary

- `{key: value}`로 이루어진 자료형

- `dic[key] = value, del dic[key]` 추가, 삭제

- key로는 immutable한 값만 사용 가능하다. 예를들면 문자열, 숫자, 튜플 등이다.


#### set(집합)

- 중복을 허용하지 않는다.

- 순서가 없다. (따라서 인덱싱을 할 수 없다.)

- `&`, `|`, `-` 등의 연산자로 각각 교집합, 합집합, 차집합을 구할 수 있다.

- `add, update, remove` 등의 메소드가 있다.


#### bool

- 자료형마다 `True`, `False`를 반환하는 조건이 있다.

  예를들면, `0`, `''`, `[]`, `{}`, `None` 등 값이 **비어있으면** `False`를 반환한다.
  반대로 `1`, `'a'`, `[1]`, `{1: 1}`, `True` 등 값이 있으면 `True`를 반환한다.


### 변수

- 실제 데이터(값)는 메모리에 저장되고, 변수는 그 값을 참조하는 주소를 가리킨다. 즉, 주소값만 가지고 있다.

- `id(변수)`를 통해 변수의 주소값을 확인할 수 있다.

- `변수A is 변수B`로 두 변수가 같은 주소를 가리키는지 확인할 수 있다.


### 제어문

#### 조건문

- `if, elif, else`로 조건문을 표현한다.
- indentation으로 블록을 구분한다.
- `value in list` 따위가 가능하다.


#### 반복문

- range 함수를 사용하면 `for i in range(10):` 처럼 사용할 수 있다.

- list comprehension을 사용하면 긴 반복문을 간단하게 표현할 수 있다.

  ```python
  a = [1, 2, 3, 4]
  result = [num * 3 for num in a if num % 2 == 0]
  print(result)  #[6, 12]

  #이중 for문도 가능하다.
  result = [x * y for x in range(2, 10)
                  for y in range(1, 10)]
  print(result)  #[2, 4, 6, ..., 81]
  ```


### 함수

- JS와 달리 **넘치거나, 부족한 인자**를 받으면 **에러**가 발생한다.

- return이 없는 경우 `None`을 반환한다.

- positional argument와 keyword argument가 있다.

- `*args, **kwargs`를 사용하면 가변인자를 받을 수 있다.

  - `*args`   : positional argument를 받는다. 함수 내에서 `args`는 tuple 타입이다.
  - `**kwargs`: keyword argument를 받는다. 함수 내에서 `kwargs`는 dictionary 타입이다.

- 리턴 타입으로 tuple, dictionary를 반환할 수 있고, 이를 unpacking해서 사용할 수 있다.
  JS의 destructuring과 비슷하다.

  ```python
  def sum_and_mul(a, b):
      return (a + b, a * b)  #또는 a + b, a * b

  sum, mul = sum_and_mul(3, 4)
  ```

- 변수의 효력은 함수 스코프로 제한된다. 즉, 경계(함수 스코프) 범위로 변수는 구분된다.

- global 키워드를 사용하면 함수 내에서 전역변수를 사용할 수 있다.

  _전역변수를 함수 내에 사용하는건 좋은 방법은 아니니, 최소화 하자._

  ```python
  a = 1
  def func():
      global a
      a += 1
      print(a)

  func()  #2
  ```

  > [!re] 주의! 예시를 통해 명확히 하자.
  >
  > **mutable 타입의 전역변수를 함수 내에서 변경하면, 함수 밖에서도 변경된 값을 유지한다.**
  >
  > 아래 예시를 보면,
  >   1. `func함수`에서 첫번째 print하는 li는 인자를 뜻한다. 그래서 아래에 함수 실행시 넘겨받은 인수 `리스트:v`를 print하고있다.
  >   2. `li = li.append(99)`에서 할당 연산자의 **좌항에** (새롭게) 선언된 변수인 `li`는 함수 내에서 유효한 변수이다.
  >      그런데 이 변수에 할당된 것은 `리스트.append 메소드`의 반환값인 `None`이다. 그래서 이후 `li`는 `None`을 리턴한다.
  >   3. `li = li.append(99)`에서 할당 연산자의 **우항에** 쓰인 `li`는 함수의 인자로 받은 `리스트:v`를 가리킨다.
  >      그래서 mutable한 타입의 리스트에 append했듯, **함수 내 구문의 효과가 함수 외부 변수에 반영된다.**
  >
  > ```python
  > v = [1, 2, 3, 4, 5]
  >
  > def func(li):
  >     print(li)    #[1, 2, 3, 4, 5]
  >
  >     li = li.append(99)
  >     return li    #None
  >
  > newV = func(v)
  > 
  > print(f'this is {newV}')  #this is None
  > print(v)   #[1, 2, 3, 4, 5, 99]
  > ```






