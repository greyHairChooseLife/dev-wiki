# 󰏢 Python



> [!cn] 개념정리
>
> 특징
>
>   - interpreter 언어다.
>   - C와 궁합이 좋다.
>   - 하나의 목적을 달성하는 하나의 문법만 존재한다. -> 근데 버전업 되면서 많이 퇴색됨
>   - 동적 타입 언어다.


## 자주 사용하는 기능

_꾸준히 조금씩 기록해 나가자._


### 내장 함수


### 표준 라이브러리


### 외부 라이브러리




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

### 타입 어노테이션 type annotation

_3.5 버전부터 적용_

- type hint를 사용하면 정적 타입을 지정할 수 있다.
  **단, 이는 타입 체크를 하지 않는다.** IDE에서 에러가 있다고 표시해주는 역할 정도다.

  ```python
  def add(a: int, b: int) -> int:
      return a + b
  ```

- `type(변수)`으로 타입을 확인할 수 있다.

  `isinstance(변수, 타입)`으로 타입 및 상속관계를 확인할 수 있다.

- object 타입은 모든 타입의 부모 타입이다. 즉, 모든 타입은 object 타입이다.


### 자료형 data type


#### None

- 아무것도 없음을 나타내는 자료형이다.
- `None`은 `False`로 간주된다.
- `is` 연산자로 `None`인지 확인할 수 있다.
  ex) `thisValue is None`


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

- `{a, b, ...}`로 표현한다.

- 중복을 허용하지 않는다.

- 순서가 없다. (따라서 인덱싱을 할 수 없다.)

- `&`, `|`, `-` 등의 연산자로 각각 교집합, 합집합, 차집합을 구할 수 있다.

- `add, update, remove` 등의 메소드가 있다.


#### bool

- 자료형마다 `True`, `False`를 반환하는 조건이 있다.

  예를들면, `0`, `''`, `[]`, `{}`, `None` 등 값이 **비어있으면** `False`를 반환한다.
  반대로 `1`, `'a'`, `[1]`, `{1: 1}`, `True` 등 값이 있으면 `True`를 반환한다.


### 변수 variable

실제 데이터(값)는 메모리에 저장되고, 변수는 그 값을 참조하는 주소를 가리킨다. 즉, 주소값만 가지고 있다.

- `id(변수)`를 통해 변수의 주소값을 확인할 수 있다.

- `변수A is 변수B`로 두 변수가 같은 주소를 가리키는지 확인할 수 있다.


### 제어문 control statement

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


### 함수 functino

- **넘치거나, 부족한 인자**를 받으면 **에러**가 발생한다.

- return이 없는 경우 `None`을 반환한다.
  JS가 `undefined`를 반환하듯이

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


- lambda 함수를 사용하면 간단한 함수를 한 줄로 표현할 수 있다.

  ```python
  add = lambda a, b: a + b
  print(add(3, 4))  #7
  ```

  때때로 유용한데, 간단한 함수를 만들어서 인수로 넘겨주는 경우 따위다.


### 사용자 입출력

- 사용자 입력

  ```python
  name = input('이름을 입력하세요.')

  print(f'안녕하세요, {name}님')
  ```

  - 입력받은 값의 타입은 `<class 'str'>`

- 사용자(에게) 출력

  - `print` 함수를 사용한다.
  - `end` 옵션을 사용할 수 있다.
    기본적으로는 `end='\n'`이다. 그래서 반복문에 사용하면 줄바꿈하며 표현되는 것.


### 파일 입출력

- `file = open('파일명', '모드')`로 사용한다.

  - 모드: `r`

    - `file.readline()`: 한줄씩 읽는다.
    - `file.readlines()`: list에 모든 줄을 담는다.
    - `file`은 iterator이다. 따라서 `for line in file:` 처럼 사용할 수도 있다.

  - 모드: `w`

    - `file.write('내용')`으로 쓴다.
    - 기존 내용을 **덮어 쓴다.**

  - 모드: `a`

    - `file.write('내용')`으로 쓴다.
    - 기존 내용 뒤에 **추가한다.**

  - **주의!!** `open`했으면 `close` 해야 한다.

    단, `with open(FILE, MODE) as my_file:` 구문을 활용해 스코프 내에서만 사용하고 자동으로 `close`할 수 있다.

    ```python
    with open('file.txt', 'r') as file:
        print(file.readline())
    ```


### 프로그램의 입출력

프로그램 자체에 외부 입력을 넣을 수 있다. 이때 sys모듈을 사용하면 편리하다.

```python
import sys

args = sys.argv[1:]
for i in args:
   print(i, end=' ')


$python file.py 100 200
# 100 200
```

- `sys.argv`의 _타입은 리스트_


### 클래스 class

일정한 (클래스)변수와 함수(메소드)를 가진 객체를 찍어내는 틀이다.

- 클래스명은 맨 앞글자를 대문자로 쓰는게 관례다.

- `self`는 객체 자신을 가리킨다. (JS의 this와 비슷하다.)

- `__init__` 메소드는 생성자이다. 객체(_클래스를 통해 생성하는 instance_)가 생성될 때 자동으로 호출된다.

- 이 밖에도 다양한 `__something__` 예약어가 존재한다. 이를 _double underscore methods_ 또는 _dunder methods_ 라고 부른다.

  인스턴스 수준에서 재할당 가능은 하지만, 굳이??

- `def method: ...` 처럼, 말줄임표로 메소드의 구현을 생략하고 인터페이스만 정의할 수 있다.


#### 상속

- `class Child(Parent):`로 상속을 받을 수 있다.

- 부모 클래스로부터 상속받은 메소드를 재정의(override)할 수 있다. 똑같은 이름으로 메소드를 정의하면 된다.

  재정의 하는 과정에서 부모 클래스의 메소드를 호출하려면 `super().메소드명()`으로 호출한다.


#### 클래스 변수

- 동일한 클래스로 찍어낸, 서로 다른 인스턴스들이 공통으로 가진 변수

- 클래스변수로 선언된 변수는 인스턴스 차원에서 재할당 될 수 있다.



### 모듈 module


이미 만들어진 python 파일을 가져다 쓰는 것이다.

```python
import 모듈_이름

# 또는
from 모듈_이름 import 모듈_함수A, 모듈_함수B
```

- `__name__`을 사용하면 모듈이 직접 실행되는지, import되어 사용되는지 구분할 수 있다.

  ```python
  if __name__ == '__main__':
      print('직접 실행')
  else:
      print('import되어 사용됨')
  ```

  즉, `__name__`은 예약어로써, 해당 구문이 현재 파일에 정의된 것인지, import된 모듈에 정의된 것이지 구분한다. import된 모듈에서 정의된 것이라면 모듈의 이름이 `__name__`이 된다.

  - 활용 예로 모듈 개발시 테스트 코드를 작성할 수 있다.

- 모듈 내에서 정의된 변수, 클래스 등은 import된 위치에서 뭐든 호출할 수 있다.
  JS와 달리 `export`를 따로 선언할 필요가 없다.
  _(이건 오히려 불편할듯? global 키워드처럼 뭔가 표현이 있으면 좋을텐데)_

- import 하려는 모듈의 경로를 추가해야할 수 있다.

  ```python
  import sys
  sys.path.append('경로')
  ```

  - 보통 모듈을 설치하면 알아서 경로가 추가된다.


### 패키지 package


패키지는 모듈의 집합이다. 모듈(파일)이 여럿 들어있는 하나의 디렉토리를 뜻한다.

e.g)
```python
my_package/
│
├── __init__.py
├── math_operations.py
└── string_operations.py

# 사용
from my_package import math_operations
...
```

- `__init__.py` 파일은 해당 디렉토리가 패키지임을 알려주는 역할을 한다.

  _(파이썬 3.3 이후부터는 없어도 되지만, 관례상 많이 사용된다.)_

  - 버전 정보를 담아둘 수 있다.

  - `__all__` 변수를 사용하면 해당 패키지에서 어떤 모듈을 import 할 수 있게 (export)할지 정할 수 있다.

    ```python
    __all__ = ['math_operations']
    ```

    > [!re] 이렇게 넣어놓지 않은 것을 import 하려고 하면 에러를 이르킨다.
    >
    > `from my_package import *` 이후에 `__all__`에 정의되지 않은 모듈을 사용하려고 하면 그렇다.
    >
    > 다만, 이럴 땐 `import my_package` 후에 my_package 자체에서 모듈 및 모듈 내 변수와 함수 등으로 접근하면 된다.


    > [!re] 잠깐! 그렇다면 3.3버전 이후 `__init__.py` 파일이 없는 경우는?
    >
    > 이때는 같은 방식으로 접근하려 해도 ImportError가 발생하지는 않는다. 다만 아무것도 불러와지지(네임스페이스에 추가되지) 않는다.
    >
    > 다만 `import my_package`로 접근해야 할 뿐이다.

    - 위와같은 이유로 `from pkg import *` 방식은 권장하지 않는다. `import pkg`를 사용하자.


### 가상환경

npm에 node_modules/가 있는 것처럼, 파이썬에도 가상환경이 있다. `python -m venv myvenv/` 하여 생성한다.

```bash
# tree -L 2 myenv/

myenv/
├── bin
│   ├── activate
│   ├── activate.csh
│   ├── activate.fish
│   ├── Activate.ps1
│   ├── pip
│   ├── pip3
│   ├── pip3.12
│   ├── python -> /usr/bin/python
│   ├── python3 -> python
│   └── python3.12 -> python
├── include
│   └── python3.12
├── lib
│   └── python3.12
├── lib64 -> lib
└── pyvenv.cfg

7 directories, 11 files
```

- `source myenv/bin/activate`를 실행하면 가상환경이 활성화된다.

  - 이렇게 가상환경에 진입하면 `pip 명령어` 등을 프로젝트 베이스로 실행할 수 있다.
  - 이 때를 기준으로 pip freeze, pip install 하며 패키지를 관리한다.
  - deactivate로 빠져나올 수 있다.

- `source myenv/bin/activate --system-site-packages`로 시스템 패키지를 가상환경에서 사용(만) 할 수 있다.

> [!rf]
> [유툽](https://www.youtube.com/watch?v=YQKxAJKMgbo)


### 예외처리 exception handling

- 아래를 부분적으로 사용한다.

  ```python
  try:
    # 예외가 발생할 수 있는 코드
  except 예외 as e:
    # (위와 같은) 예외일 때 코드
  else:
    # 예외 발생하지 않았을 때 코드
  finally:
    # 항상 실행하는 코드
  ```

  - except 뒤에 아무 예외도 지정하지 않으면 모든 예외를 받아낸다.


- `raise`로 예외를 임의 발생시킬 수 있다.

  ```python
  class BasePersonClass:
    ...
    def say_nick(nick):
        if nick == '바보':
            raise MyError()
  ```

  ex) 클래스를 상속받을 때 자식 클래스에서 메소드를 반드시 재정의(구현)하도록 강제할 수도 있다.
  ex) 임의의 에러 조건을 만들어낼 때 사용한다.


### 내장함수 built-in function

필요한거 찾아서 쓰자~


### 표준 라이브러리 standard library

**따로 설치할 필요 없이,** import만 해서 사용할 수 있는 라이브러리다.

유명한거 몇개 알아두고 쓰자~


### 외부 라이브러리

파이썬 모듈이나 패키지를 외부에서 가져와 사용하는 것이다.

- 'pypi.org'에서 다양한 패키지를 찾을 수 있다.
- `pip`가 대표적인 패키지 관리자다.

  - pip list: 설치된 패키지 목록을 보여준다.
  - pip show 패키지명: 패키지 정보를 보여준다.
  - pip install 패키지명: 설치
  - pip uninstall 패키지명: 삭제

  - `requirements.txt` 파일을 만들어서 관리할 수 있다.

    ```bash
    pip freeze > requirements.txt # 현재 설치된 패키지 목록을 requirements.txt에 저장
    pip install -r requirements.txt
    ```

    - `list` vs `freeze`

      | 특징      | `freeze`                                         | `list`                           |
      |-----------|--------------------------------------------------|----------------------------------|
      | 출력 형식 | 패키지명==버전                                   | 표 형식(패키지명, 버전)          |
      | 용도      | 패키지 버전을 고정한 재현, 동일 환경 공유를 위해 | 설치된 패키지 확인               |
      | 파일 저장 | 일반적으로 > requirements.txt 생성에 사용        | 파일 저장 용도로는 잘 사용 안함  |
      | 옵션      | 없음                                             | --outdated, --uptodate 등 다양함 |


## 심화 주제

### 파이썬과 유니코드

_0과 1로 인간의 문자를 표현하는 방법이다._

_아스키 코드가 맨 처음 시도였고, 세계 각국의 문자를 표현하기 위해 유니코드가 등장했다._


- 파이썬 3부터는 모든 문자열을 유니코드로 처리한다.

  - 인코딩: 문자열 -> 바이트

    ```python
    '문자열'.encode('utf-8')
    '문자열'.encode('euc-kr')
    '문자열'.encode('ascii')  #에러, 아스키 코드에는 '한글'이 없다.
    ```

    - 타입을 확인해보면 바이트로 변환됐음을 알수 있다.
      `type(b'문자열')` -> `<class 'bytes'>`

  - 디코딩: 바이트 -> 문자열

    ```python
    b'\xec\x9d\x98\xeb\xac\xb8\xec\x9e\x90\xec\x97\xb4'.decode('utf-8')
    ```

    - 인코딩 된 방식으로 디코딩 해야한다.

- 파일을 쓰고, 읽을 때 인코딩을 지정할 수 있다. 인코딩이 상이하여 데이터를 제대로 읽지 못할 수 있다.

  utf-8이 널리 쓰이는 인코딩 방식이고, 파이썬 3부터는 기본적으로 utf-8을 사용하기 때문에 딱히 안건드려도 문제가 되는 경우는 잘 없다.

- '소스 코드'도 사람이 읽기 쉽게 인코딩한 것이기 때문에 컴퓨터가 이해할 수 있도록 인코딩을 지정해줘야 한다.

  파일 최상단에 아래와 같이 적어주면 된다.

  ```python
  # -*- coding: utf-8 -*-
  ```

  물론, 파이썬 3버전 부터는 기본적으로 utf-8을 사용하기 때문에 따로 적어주지 않아도 된다.


### 클로저와 데코레이터 closure, decorator

- closure: 함수 내부에 함수를 정의하고, 그것을 반환하는 함수

- decorator: 기존 함수를 변형하지 않고 기능을 추가(wrapping)해주는 함수, closure의 한 종류

  ```python
  def my_decorator(func):
      def wrapper():
          print('함수 시작')
          func()
          print('함수 끝')
      return wrapper

  @my_decorator
  def hello():
      print('hello')

  hello()

    #함수 시작
    #hello
    #함수 끝
  ```

  위와같이 `@decorator`를 사용해서 쉽게 감싸줄 수 있다.


### 이터레이터, 제너레이터 iterator, generator

- iterator: 반복 가능한 객체, `next(이터레이터)`를 호출할 수 있다.

  - 클래스에 `__iter__`, `__next__` 메소드를 구현하면 iterator 객체로 사용할 수 있다.

  - iterable한 객체와 iterator 객체는 다르다.

    예를들어 `list`는 반복 가능한 객체이지만, `next`를 호출할 수 없다.

  - `iter(iterable객체)` 함수로 iterator 객체로 _변환할 수 있다._

  - `next`는 하나씩 값을 꺼내오는 함수인데, 한번 꺼냈으면 끝이다. 다시 처음부터 꺼내오려면 다시 `iter`를 호출해야 한다.


- generator: iterator를 생성하는 함수

  - `yield`를 사용하면 함수를 호출한 곳으로 값을 반환하고, 함수의 _상태를 유지한다._
    상태를 유지한다는 것이 중요한데, next를 호출해서 한번 꺼냈으면 끝이라는 사실을 구현한다.

  - 반복문을 통해 yeild로 값을 반환하며 iterator를 만들 수 있다.
  - list를 만들 때 `[]`를 사용한 것처럼, 대신 `()`를 사용하면 generator를 만들 수 있다.

    ```python
    my_list = [i for i in range(10)]

    my_gen = (i for i in range(10))
    ```

- 활용 예

  어떤 함수가 결과를 반환하는데 10초가 소요된다고 가정해보자. 이 함수를 10번 실행해서 결과를 리스트에 담는다면 전부 100초가 걸릴 것이다.

  그런데 이 때 특정 코드라인에서 이 리스트에서 0번째 값만 필요하다면, 0번재 값만 생성되었을 때 실행할 수 있으면 좋다.

  이때 그냥 리스트를 생성하면 100초를 기다렸다가 0번째 값을 사용해야하지만, generator를 사용하면 0번째 값만 생성되었을 때 `next()`를 통해 실행할 수 있다.



## 레퍼런스 reference

[조코딩 2024' 무료강의](https://www.youtube.com/watch?v=ftQZo7XaTOA)
