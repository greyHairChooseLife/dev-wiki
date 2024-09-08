# Learn dart

## why dart?

### dart native: almost everywhere

- Just In Time (JIT)

: instantly updated UI for developing experience

- Adhead Of Time (AOT)

: compile to binary code for performance

- null satisfaction

### dart web: interpreted to Javascript

### <p style="background: green; padding-left: 10px">Basic</p>

<details>
  <summary><h4 style="color: white; display: inline-block;"><span style="color: grey;">Type of Variable: </span>var vs [type] vs dynamic</h4></summary>
  <div style="background-color: #E2EADD; padding: 10px; color: black">

변수의 타입을 지정한다.(값은 재할당 가능한 것이 default다.)

1. var: 알아서 추론한다. 한번 추론 된 타입은 변하지 않는다.
2. String, int, double, boolean, ... : 하나 지정한다. 타입은 변하지 않는다.
3. dynamic: 타입이 변경 될 수 있다. 즉, 다른 타입의 값이 재할당 될 수 있다.

기본적으로 타입을 지정해서 쓰고, 필요에 따라 dynamic을 쓰도록 하자. var같은건 쓸 일이 있을까?

> Ref: youtube 코드팩토리' [무료 프로그래밍 강의] 1시간만에 끝내는 Dart언어 기본기 https://www.youtube.com/watch?v=3Ck42C2ZCb8

  </div>
</details>

<details>
  <summary><h4 style="color: white; display: inline-block;"><span style="color: grey;">Constant Variable: </span>final vs const</h4></summary>
  <div style="background-color: #E2EADD; padding: 10px; color: black">

상수인 변수를 선언한다.

그러나,

1. final 변수에는 build-time, run-time에 결정되는 값을 할당 할 수 있다.
2. const 변수에는 build-time에 결정되는 값을 할당 할 수 있다. run-time에 결정되는 값을 할당 할 수 없다.

<pre style="padding-top: 0;"><code style="color: white;">
const now = Datetime.now(); // error
</code></pre>

> Ref: youtube 코드팩토리' [무료 프로그래밍 강의] 1시간만에 끝내는 Dart언어 기본기 https://www.youtube.com/watch?v=3Ck42C2ZCb8

  </div>
</details>

<details>
  <summary><h4 style="color: white; display: inline-block;"><span style="color: grey;">(non-)NULL Ability: </span>var? vs var!</h4></summary>
  <div style="background-color: #E2EADD; padding: 10px; color: black">

nullability 여부에 Javascript 보다 깐깐하다.

1. var?: null 가능

<pre style="padding-top: 0;"><code style="color: white;">
String name? = 'sangyeon';
name = null; // fine
</code></pre>

2. var!: null 불가능

> Ref: youtube 코드팩토리' [무료 프로그래밍 강의] 1시간만에 끝내는 Dart언어 기본기 https://www.youtube.com/watch?v=3Ck42C2ZCb8

  </div>
</details>

<details>
  <summary><h4 style="color: white; display: inline-block;"><span style="color: grey;">Set of Variables: </span>List, Map, Set</h4></summary>
  <div style="background-color: #E2EADD; padding: 10px; color: black">

1. List\<Type>
2. Map\<key-type, value-type>
3. Set\<Type> : List랑 똑같은데, 중복 제거 해 준다.

> Ref: youtube 코드팩토리' [무료 프로그래밍 강의] 1시간만에 끝내는 Dart언어 기본기 https://www.youtube.com/watch?v=3Ck42C2ZCb8

  </div>
</details>

<details>
  <summary><h4 style="color: white; display: inline-block;"><span style="color: grey;"></span>enum</h4></summary>
  <div style="background-color: #E2EADD; padding: 10px; color: black">


<pre style="padding-top: 0;"><code style="color: white;">
enum Status {
  approved,
  pending,
  rejected,
}

void main() {
Status status = Status.approved;

if (status == Status.approved) {
print('승인');
} else if (status == Status.pending) {
print('대기');
} else {
print('거절');
}
}
</code></pre>

> Ref: youtube 코드팩토리' [무료 프로그래밍 강의] 1시간만에 끝내는 Dart언어 기본기 https://www.youtube.com/watch?v=3Ck42C2ZCb8

  </div>
</details>

<details>
  <summary><h4 style="color: white; display: inline-block;"><span style="color: grey;">Parameter of Function: </span>positional, optional, named</h4></summary>
  <div style="background-color: #E2EADD; padding: 10px; color: black">

1. positional
    : 순서대로 드간다.

2. optional
<pre style="padding-top: 0;"><code style="color: white;">
  addNumbers(int x, [int y = 0]) {
    return x + y;
  }

print(addNumbers(30)); // 30, no error occurred
</code></pre>

3. named

<pre style="padding-top: 0;"><code style="color: white;">
  int addNumbers({
    required int x,
    required int y,
    int z = 5, // optional
  }) {
    return x + y + z;
  }

  print(addNumbers(y: 30, x: 10));  // 45
</code></pre>

> Ref: youtube 코드팩토리' [무료 프로그래밍 강의] 1시간만에 끝내는 Dart언어 기본기 https://www.youtube.com/watch?v=3Ck42C2ZCb8

  </div>
</details>

<details>
  <summary><h4 style="color: white; display: inline-block;"><span style="color: grey;">Define Type: </span>typedef</h4></summary>
  <div style="background-color: #E2EADD; padding: 10px; color: black">

<pre style="padding-top: 0;"><code style="color: white;">
void main() {
  final int result = calc(5, 10, 20, add);

print(result); // 35
}

typedef Operation = int Function(int x, int y, int z);

int add(int x, int y, int z) => x + y + z;
int subtract(int x, int y, int z) => x - y - z;

// 이 부분의 표현력의 핵심
// 또한 operation 부분의 타입을 지정하지 않으면 리턴 타입이 dynamic하게 된다. run-time에 오류 가능성 있음.
int calc(int x, int y, int z, Operation operation) => operation(x, y, z);
</code></pre>

> Ref: youtube 코드팩토리' [무료 프로그래밍 강의] 1시간만에 끝내는 Dart언어 기본기 https://www.youtube.com/watch?v=3Ck42C2ZCb8

  </div>
</details>

---

### <p style="background: green; padding-left: 10px">OOP</p>

<details>
  <summary><h4 style="color: white; display: inline-block;"><span style="color: grey;">Class: </span>constructor</h4></summary>
  <div style="background-color: #E2EADD; padding: 10px; color: black">

여러 방법이 있다.

1. <pre style="padding-top: 0;"><code style="color: white;">
      class Student1 {
        String name;
        int age;

        Student1(String name, int age)
          : this.name = name,
            this.age = age;
      }

      // instance 생성 예시
      Student1 stu1 = Student1("Tom", 18);
   </code></pre>

2. <pre style="padding-top: 0;"><code style="color: white;">
      class Student2 {
        String name;
        int age;

        Student2(this.name, this.age)
      }

      // instance 생성 예시
      Student2 stu2 = Student2("Tom", 18);
   </code></pre>

3. <pre style="padding-top: 0;"><code style="color: white;">
      class Student3 {
        String name;
        int age;

        Student3.fromList(List values)
            : name = values[0],
              age = values[1];
      }

      // instance 생성 예시
      Student3 stu3 = Student3.fromList(["Tom", 18]);
   </code></pre>

기본적으로는 2.번의 방법을 사용하되, 만약 이미 생성한 변수의 형태를 통해 새로운 객체를 construct 해야한다면, ClassName.fromSomethingI() 등의 방법을 생각 해 볼 수 있을 듯.

> Ref: youtube 코드팩토리' [무료 프로그래밍 강의] 1시간만에 끝내는 객체지향 프로그래밍 https://www.youtube.com/watch?v=7e80Il_7Z70&ab_channel=%EC%BD%94%EB%93%9C%ED%8C%A9%ED%86%A0%EB%A6%AC

  </div>
</details>

<details>
  <summary><h4 style="color: white; display: inline-block;"><span style="color: grey;">Class: </span>immutable</h4></summary>
  <div style="background-color: #E2EADD; padding: 10px; color: black">

어떤 class의 인스턴스를 생성하였다면, 이것의 member variable는 변경하지 않는 것이 권장 된다. 메모리 걱정 말고 새로운 인스턴스를 생성하여 사용하자. 불변성을 지키고 에러 가능성을 낮추는 것이 남는 장사다.

이를 위해 class의 member variable는 final 키워드를 사용하여 선언 해 주는 것이 좋다. 즉, final은 일단 붙이고 나서 꼭 필요한 경우에만 떼어낸다.

<pre style="padding-top: 0;"><code style="color: white;">
ex)

void main() {
  Student student = Student("Tom", 18);

  student.age = 22; // error since it's with 'final'
}
  class Student {
    final String name;
    final int age;

    Student(this.name, this.age)
  }
</code></pre>

> Ref: youtube 코드팩토리' [무료 프로그래밍 강의] 1시간만에 끝내는 객체지향 프로그래밍 https://www.youtube.com/watch?v=7e80Il_7Z70&ab_channel=%EC%BD%94%EB%93%9C%ED%8C%A9%ED%86%A0%EB%A6%AC

  </div>
</details>

<details>
  <summary><h4 style="color: white; display: inline-block;"><span style="color: grey;">Class: </span>inheritance</h4></summary>
  <div style="background-color: #E2EADD; padding: 10px; color: black">

super keyword를 활용 한다.

<pre style="padding-top: 0;"><code style="color: white;">
class Man {
  final String name;
  final int age;

  Man(this.name, this.age);
}

class Student extends Man {
  final String grade;

  Student(String name, int age, this.grade) : super(name, age);
}
</code></pre>

> Ref: youtube 코드팩토리' [무료 프로그래밍 강의] 1시간만에 끝내는 객체지향 프로그래밍 https://www.youtube.com/watch?v=7e80Il_7Z70&ab_channel=%EC%BD%94%EB%93%9C%ED%8C%A9%ED%86%A0%EB%A6%AC

  </div>
</details>

<details>
  <summary><h4 style="color: white; display: inline-block;"><span style="color: grey;">Class: </span>override method</h4></summary>
  <div style="background-color: #E2EADD; padding: 10px; color: black">

super keyword를 활용 한다.

<pre style="padding-top: 0;"><code style="color: white;">
class TimesTwo {
  final int number;

  TimesTwo(this.number);

  int calculate() {
    return number * 2;
  }
}

class TimesFour extends TimesTwo {
  TimesFour(int number) : super(number);

  @override
  int calculate() {
    return super.calculate() * 2;   // super키워드 생략 가능하지만 명시적으로 쓰는게 좋다.
  }
}
</code></pre>

> Ref: youtube 코드팩토리' [무료 프로그래밍 강의] 1시간만에 끝내는 객체지향 프로그래밍 https://www.youtube.com/watch?v=7e80Il_7Z70&ab_channel=%EC%BD%94%EB%93%9C%ED%8C%A9%ED%86%A0%EB%A6%AC

  </div>
</details>

<details>
  <summary><h4 style="color: white; display: inline-block;"><span style="color: grey;">Class: </span>static</h4></summary>
  <div style="background-color: #E2EADD; padding: 10px; color: black">

static은 instance에 귀속되지 않고, class에 귀속된다.

<pre style="padding-top: 0;"><code style="color: white;">
void main() {
  Number.defaultValue = 100;
}

class Number {
  static int? defaultValue;
}
</code></pre>

> Ref: youtube 코드팩토리' [무료 프로그래밍 강의] 1시간만에 끝내는 객체지향 프로그래밍 https://www.youtube.com/watch?v=7e80Il_7Z70&ab_channel=%EC%BD%94%EB%93%9C%ED%8C%A9%ED%86%A0%EB%A6%AC

  </div>
</details>

<details>
  <summary><h4 style="color: white; display: inline-block;"><span style="color: grey;">Class: </span>interface</h4></summary>
  <div style="background-color: #E2EADD; padding: 10px; color: black">

class의 형태를 얻어 온다. 어떤 클래스를 interface로 만드는 것이 아니라 새로운 클래스를 만들 때 기존에 있는 다른 클래스를 interface 삼는 것이다.

interface로 삼은 기존 클래스에 존재하는 맴버변수나 메소드를 빠뜨리기라도 하면 에러를 이르킨다.

한편, 오로지 interface로 활용하기 위한 목적으로만 만드는 class는 abstract 키워드를 달아준다. 그러면 해당 class로 instance를 만들어내지 못하도록 막아준다.

또한 interface 삼은 기존 class도 타입으로 인정된다.(`new Nunu('apple', 1) is IdolInterface // true`)

<pre style="padding-top: 0;"><code style="color: white;">
abstract class IdolInterface {
  String name;

  IdolInterface(this.name);

  void sayName() {}
}

class Nunu implements IdolInterface {
  String name;
  int number;

  Nunu(this.name, this.number);

  void sayName() {
    print('My name is $name');
  }
}
</code></pre>

> Ref: youtube 코드팩토리' [무료 프로그래밍 강의] 1시간만에 끝내는 객체지향 프로그래밍 https://www.youtube.com/watch?v=7e80Il_7Z70http://localhost:9072/page/1ab_channel=%EC%BD%94%EB%93%9C%ED%8C%A9%ED%86%A0%EB%A6%AC

  </div>
</details>

<details>
  <summary><h4 style="color: white; display: inline-block;"><span style="color: grey;">Class: </span>generic</h4></summary>
  <div style="background-color: #E2EADD; padding: 10px; color: black">

class 맴버 변수의 타입을 instance를 생성하면서 지정한다.(class를 정의 할 때가 아니라)

> Ref: youtube 코드팩토리' [무료 프로그래밍 강의] 1시간만에 끝내는 객체지향 프로그래밍 https://www.youtube.com/watch?v=7e80Il_7Z70&ab_channel=%EC%BD%94%EB%93%9C%ED%8C%A9%ED%86%A0%EB%A6%AC

  </div>
</details>

---

### <p style="background: green; padding-left: 10px">Functional programming</p>

<a href="https://www.youtube.com/watch?v=fwh27A_D-20&ab_channel=%EC%BD%94%EB%93%9C%ED%8C%A9%ED%86%A0%EB%A6%AC">youtube</a>

---

### <p style="background: green; padding-left: 10px">Asynchronous programming</p>

<a href="https://www.youtube.com/watch?v=rk41rBXq3zQ&ab_channel=%EC%BD%94%EB%93%9C%ED%8C%A9%ED%86%A0%EB%A6%AC">youtube</a>

---

### <p style="background: green; padding-left: 10px">\*Reference</p>

1. source: youtube
   title: [무료 프로그래밍 강의] 1시간만에 끝내는 Dart언어 기본기
   ref: https://www.youtube.com/watch?v=3Ck42C2ZCb8

2. source: youtube
   title: [무료 프로그래밍 강의] 1시간만에 끝내는 객체지향 프로그래밍
   ref: https://www.youtube.com/watch?v=7e80Il_7Z70&t=0s
