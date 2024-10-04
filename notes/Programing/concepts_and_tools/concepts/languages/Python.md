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


#### 숫자형: 정수, 실수 등

연산자에는 `+`, `-`, `*`, `/`, `//`, `%`, `**`가 있다.

- `//`: 몫만 얻는다.
- `**`: 제곱을 구한다.


#### 문자형

문자열은 `''`, `""`, `''' '''`, `""" """`로 표현한다.

- `\` 이스케이핑도 가능하다. `\n` 줄바꿈, `\t` 탭 등 특수 이스케이핑도 가능하다.

- 문자열에 `*` 연산자를 사용가능
  ex) `print('1' * 10)` -> `1111111111`

- 문자열에 `[n]` 으로 인덱싱 가능

- `[이상:미만]`, `[이상:미만:간격]` 슬라이싱 가능

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

- 문자열 포맷팅

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


...
1:37:31 까지 시청
