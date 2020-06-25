# CPP 전체적인 개념 정리

---

# OOP(Object-Oriented Programing)
컴퓨터 프로그래밍의 패러다임  
프로그램을 명령어의 목록이 아닌, 독립된 "객체"들의 모임으로 바라봄

객체란, 변수들과 참고 자료들로 이루어진 소프트웨어 덩어리  

특징  
- **추상화 (abstraction)**
    - 대상의 특성 중 불필요한 부분을 무시하고 필요한 공통점만을 다루어, 현실의 복잡성을 극복하고 목적에 집중할 수 있도록 하는 것

    - 공통의 속성이나 기능을 묶어 이름을 붙이는 것

    - Class를 정의하는 것

- **캡슐화 (Encapsulation)**  
    - 객체 스스로가 자신의 상태를 책임지게 하여, 해당 객체의 역할 수행에 집중할 수 있도록 자율성을 높이는 것

    - 데이터 구조와 데이터를 다루는 방법들을 결합시켜 묶는 것

    - 변수와 함수를 하나로 묶는 것

    - 데이터를 외부에서 접근하는 것을 방지하고, 오로지 함수를 통해서만 접근할 수 있게 하는 것

    - 캡슐화의 2가지 관점

        1) 상태-행위 캡슐화 (데이터 캡슐화) : 객체는 상태와 행동을 하나의 단위로 묶는 자율적 실체

        2) 사적인 비밀의 캡슐화 (은닉화) : 외부에서 객체의 상태를 변경할 수 없도록 은닉

    - 접근 변경자

        public > protected > default > private

        모든 접근 허용 > default + 상속 관계인 다른 패키지의 클래스 > 같은 패키지 > 같은 클래스  

- **상속성 (Ingeritance)**  

    - 부모 클래스의 속성과 기능을 상속받아 동일하게 사용하는 것

    - 범용적인 클래스를 작성한 후 상속을 활용하면, 여러 클래스에서 중복되는 속성과 기능을 재사용할 수 있음

- **다형성 (Polymorphism)**

    - 동일 요청에 대해 서로 다른 방식으로 응답할 수 있도록 만드는 것

    - 오버라이딩 (Overriding) : 상속받은 동일한 메소드 재정의

    - 오버로딩 (Overloading) : 동일한 메소드가 매개변수 타입, 개수 차이에 따라 다르게 동작 

C++ 에서 클래스를 이용해서 만들어진 객체를 **인스턴스(instance)** 라고 부름  

---

# 복사 생성자 (copy constructor)
```
T(const T& a);
```
다른 T 의 객체 a 를 상수 레퍼런스로 받음  
여기서 a 가 const 이기 때문에 우리는 복사 생성자 내부에서 a 의 데이터를 변경할 수 없고,  
오직 새롭게 초기화 되는 인스턴스 변수들에게 '복사' 만 할 수 있게 됨  

```
Photon_Cannon::Photon_Cannon(const Photon_Cannon& pc) {
  std::cout << "복사 생성자 호출 !" << std::endl;
  hp = pc.hp;
  shield = pc.shield;
  coord_x = pc.coord_x;
  coord_y = pc.coord_y;
  damage = pc.damage;
}
```
위와 같이 복사 생성자 내부에서 pc 의 인스턴스 변수들에 접근해서 객체의 shield, coord_x, coord_y 등을 초기화 할 수 는 있지만

`pc.coord_x = 3;` 처럼 pc 의 값 자체는 변경할 수 없다는 이야기

`Photon_Cannon pc2 = pc1;`  
위 코드 역시 복사 생성자가 호출  
C++ 컴파일러는 위 문장을 아래와 동일하게 해석  
`Photon_Cannon pc2(pc1);`  

C++ 컴파일러는 디폴트 복사 생성자를 지원, 이는 대응되는 원소들을 1 대 1 복사를 해줌

## 디폴트 복사 생성자의 한계
만약 초기화되는 멤버 변수 중 포인터가 있을때 디폴트 복사 생성자를 사용하면  
기존의 클래스에 있는 포인터와 새로 생성된 클래스에 있는 포인터가 같은 값을 가지게 됨  

이 때, 하나의 클래스가 소멸하며 포인터에 할당되어있던 메모리를 해제하면 남은 클래스에서는 해제된 메모리 주소를 가리키고 있음  
이런 상태에서 남은 클래스의 포인터를 해제시키려하면 이미 해제된 메모리에 접근하기때문에 런타임 오류가 발생  

이를 막기 위해서는 해당 포인터를 그대로 복사하지 말고 따로 다른 메모리에 동적 할당을 해서 그 내용만 복사하면 됨  

이렇게 메모리를 새로 할당해서 내용을 복사하는 것을 **깊은 복사(deep copy)**,  
단순히 대입만 해주는 것을 **얕은 복사(shallow copy)** 라고 함

컴파일러가 생성하는 디폴트 복사 생성자의 경우 얕은 복사밖에 할 수 없으므로 깊은 복사가 필요한 경우에는 사용자가 직접 복사 생성자를 만들어야함  

---

# explict
```
void DoSomethingWithString(MyString s) {
  // Do something...
}
```
위 코드는
```
DoSomethingWithString(MyString("abc"))
```
MyString 객체를 생성해서 이를 인자로 전달  

MyString을 명시적으로 생성하지 않는 경우
```
DoSomethingWithString("abc")
```

"abc"는 MyString 타입이 아니지만 컴파일러는 생성자 중에서 이를 MyString으로 변환할 수 있는지 찾음
```
// 문자열로 부터 생성
MyString(const char* str);
```

MyString에는 const char* 로부터 MyString을 만드는 생성자가 있으므로  
`DoSomethingWithString("abc")`는 알아서 `DoSomethingWithString(MyString("abc"))`로 변환되어 컴파일됨  

위와 같은 변환을 **암시적 변환(implicit conversion)** 이라고 부름  

암시적 변환이 항상 사용자에세 편리한 것은 아님  

```
DoSomethingWithString(3)
```
이는 아마도 함수를 사용자가 잘못 사용했을 가능성이 높음  
문자열을 받는 함수에 정수를 입력했기 때문  
하지만 컴파일러는 위 문장을 오류로 판단하지 않음  

```
// capacity 만큼 미리 할당함.
MyString(int capacity);
```
위와 같이 int 인자를 받는 MyString 생성자가 있다면 위 함수는

```
DoSomethingWithString(MyString(3))
```
으로 변환되어 컴파일됨  
즉, 사용자가 의도하지 않은 암시적 변환이 일어나게 됨  

이러한 의도하지 않는 암시적 변환을 막기위해 사용하는 키워드가 **explicit**  

```
// capacity 만큼 미리 할당함. (explicit 키워드에 주목)
explicit MyString(int capacity);
```

explicit 은 또한 해당 생성자가 복사 생성자의 형태로도 호출되는 것을 막게 됨

```
MyString s(5);   // 허용
MyString s = 5;  // 컴파일 오류!
```

위와 같이 explicit을 이용해 생성자를 선언해두면 명시적으로 생성자를 부를 때 에만 허용할 수 있게 됨

# mutable
mutable로 선언하면 const 함수에서도 이들 값을 바꿀 수 있음  

```
#include <iostream>

class A {
  mutable int data_;

 public:
  A(int data) : data_(data) {}
  void DoSomething(int x) const {
    data_ = x;  // 가능!
  }

  void PrintData() const { std::cout << "data: " << data_ << std::endl; }
};

int main() {
  A a(10);
  a.DoSomething(3);
  a.PrintData();
}
```

왜 mutable을 사용하나?  
멤버 함수를 const 로 선언하는 의미는 **이 함수는 객체의 내부 상태에 영향을 주지 않습니다** 를 표현하는 방법  
**읽기** 작업을 수행하는 함수들이 그 예  

대부분의 경우 의미상 상수 작업을 하는 경우, 실제로도 상수 작업을 하게 되나, 실제로 꼭 그렇지만은 않음  

예를 들어, 아래와 같은 서버 프로그램을 만든다고 가정했을 때
```
class Server {
  // .... (생략) ....

  // 이 함수는 데이터베이스에서 user_id 에 해당하는 유저 정보를 읽어서 반환한다.
  User GetUserInfo(const int user_id) const {
    // 1. 데이터베이스에 user_id 를 검색
    Data user_data = Database.find(user_id);

    // 2. 리턴된 정보로 User 객체 생성
    return User(user_data);
  }
};
```
이 서버에는 GetUserInfo 라는 함수가 있는데 입력 받은 user_id 로 데이터베이스에서 해댱 유저를 조회해서 그 유저의 정보를 리턴하는 함수  
당연히도 데이터베이스를 업데이트 하지도 않고, 무언가 수정하는 작업도 당연히 없기 때문에 const 함수로 선언되어 있음  

그런데 대개 데이터베이스에 요청한 후 받아오는 작업은 꽤나 오래 걸림  
그래서 보통 서버들의 경우 메모리에 캐쉬(cache)를 만들어서 자주 요청되는 데이터를 굳이 데이터베이스까지 가서 찾지 않아도 메모리에서 빠르게 조회할 수 있도록 함

물론 캐쉬는 데이터베이스만큼 크지 않기 때문에 일부 유저들 정보 밖에 포함하지 않음 따라서 캐쉬에 해당 유저가 없다면, 데이터베이스에 직접 요청해야됨  
대신 데이터베이스에서 유저 정보를 받으면 캐쉬에 저장해놓아서 다음에 요청할 때는 빠르게 받을 수 있게 됨  

이를 구현하면
```
class Server {
  // .... (생략) ....

  Cache cache; // 캐쉬!

  // 이 함수는 데이터베이스에서 user_id 에 해당하는 유저 정보를 읽어서 반환한다.
  User GetUserInfo(const int user_id) const {
    // 1. 캐쉬에서 user_id 를 검색
    Data user_data = cache.find(user_id);

    // 2. 하지만 캐쉬에 데이터가 없다면 데이터베이스에 요청
    if (!user_data) {
      user_data = Database.find(user_id);

      // 그 후 캐쉬에 user_data 등록
      cache.update(user_id, user_data); // <-- 불가능
    }

    // 3. 리턴된 정보로 User 객체 생성
    return User(user_data);
  }
};
```

문제는 GetUserInfo 가 const 함수라는 점, 따라서 
```
cache.update(user_id, user_data);  // <-- 불가능
```

위 처럼 캐쉬를 업데이트 하는 작업을 수행할 수 없음  
왜냐하면 캐쉬를 업데이트 한다는 것은 케쉬 내부의 정보를 바꿔야 된다는 뜻이기 때문  

따라서 저 update 함수는 const 함수가 아니어야 함  
그렇다고 해서 GetUserInfo 에서 const 를 뗄 수 없는 것이,  
이 함수를 사용하는 사용자의 입장에선 당연히 const 로 정의되어야 하는 함수임  

따라서 이 경우, Cache 를 mutable 로 선언해버리면 됨

---

# 상속
## 업 캐스팅, 다운 캐스팅
Parent 클래스를 상속받는 Child 클래스가 있을 때  
```
Child c;
Parent* p = &c;
```
위와 같은 선언이 가능  

Child 는 Parent 를 상속받았기 때문에 Parent가 가지고 있는 멤버 변수, 멤버 함수를 모두 포함하고 있음  
따라서 별 이상 없이 실행 가능  

이렇게 자식 클래스에서 부모 클래스로 캐스팅 하는 것을 **업 캐스팅** 이라고 하고,  
이와 반대로 부모 클래스에서 자식 클래스로 캐스팅 하는 것을 **다운 캐스팅** 이라고 함  

다운 캐스팅에서 Parent 는 Child 가 가지고 있는 멤버 변수, 멤버 함수를 모르기 때문에  
컴파일러상 함부로 다운 캐스팅 하는 것을 금지하고 있음(컴파일 에러가 발생)  

```
Parent p;
Child* c = static_cast<child*>(p);
```

위와 같이 강제로 다운 캐스팅이 가능하나, 다운 캐스팅 이후 Child 에서 선언, 혹은 오버라이드 한 함수나 변수에 접근한다면  
런타임 에러가 발생함  

따라서 강제적으로 다운 캐스팅을 하는 경우, 컴파일 타임에서 오류를 찾아내기 매우 힘들기 때문에  
작동이 보장되지 않는 다운 캐스팅은 권장하지 않음

## vitural
```
class Parent {
 public:
  virtual void what() { cout << "Parent what()\n"; }
};

class Child : public Parent {
 public:
  void what() { cout << "Child what()\n"; }
};

int main(){
  Parent p;
  Child c;

  Parent* p_p = &p;
  Parent* p_c = &c;

  p_p->what();
  p_c->what();
}
```
virtual 키워드를 사용하면 위와 같은 상황에서 적절한 함수를 호출해줌  
virtual 키워드는 컴파일 시에 어떤 함수가 실행될 지 정해지지 않고 런타임 시에 정해줌  
이러한 일을 **동적 바인딩(dynamic binding)** 이라고 부름  

이러한 virtual 키워드가 붙은 함수를 **가상 함수(virtual function)** 라고 부르고,  
파생 클래스의 함수가 기반 클래스의 함수를 오버라이드 하기 위해서는 두 함수의 꼴이 정확히 같아야함  

C++ 11 에서는 파생 클래스에서 기반 클래스의 가상 함수를 오버라이드 하는 경우,  
override 키워드를 통해서 명시적으로 나타낼 수 있음  
override 키워드를 사용하게 되면, 실수로 오버라이드를 하지 않는 경우를 막을 수 있음

## virtual 소멸자
```
#include <iostream>
using namespace std;

class Parent {
 public:
  Parent() { cout << "Parent 생성자 호출\n"; }
  ~Parent() { cout << "Parent 소멸자 호출\n"; }
};
class Child : public Parent {
 public:
  Child() : Parent() { cout << "Child 생성자 호출\n"; }
  ~Child() { cout << "Child 소멸자 호출\n"; }
};

int main() {
  cout << "--- 평범한 Child 만들었을 때 ---\n";
  { Child c; }
  cout << "--- Parent 포인터로 Child 가리켰을 때 ---\n";
  {
    Parent *p = new Child();
    delete p;
  }
}
```
위 코드를 실행시키면

```
--- 평범한 Child 만들었을 때 ---
Parent 생성자 호출
Child 생성자 호출
Child 소멸자 호출
Parent 소멸자 호출
--- Parent 포인터로 Child 가리켰을 때 ---
Parent 생성자 호출
Child 생성자 호출
Parent 소멸자 호출
```
와 같은 결과가 나옴  

위와 같은 상황에서 문제는 Parent 포인터가 Child 객체를 가리킬 때에서  
delete p 를 하더라도, p 가 가리키는 것은 Parent 객체가 아닌 Child 객체 이기 때문에,  
위에서 보통의 Child 객체가 소멸되는 것과 같은 순서로 생성자와 소멸자들이 호출되어야만 함  
하지만 실제로는, Child 소멸자가 호출되지 않음  

소멸자가 호출되지 않는다면 여러가지 문제가 생길 수 있음  
예를 들어서, Child 객체에서 메모리를 동적으로 할당하고 소멸자에서 해제하는데,  
소멸자가 호출 안됬다면 메모리 누수(memory leak)가 생기게 됨  

이 상황의 해결책은 Parent 의 소멸자를 virtual 로 만들어버리면 됨  
Parent 의 소멸자를 virtual 로 만들면, p 가 소멸자를 호출할 때, Child 의 소멸자를 성공적으로 호출할 수 있게 됨

```
#include <iostream>
using namespace std;

class Parent {
 public:
  Parent() { cout << "Parent 생성자 호출\n"; }
  virtual ~Parent() { cout << "Parent 소멸자 호출\n"; }
};
class Child : public Parent {
 public:
  Child() : Parent() { cout << "Child 생성자 호출\n"; }
  ~Child() { cout << "Child 소멸자 호출\n"; }
};

int main() {
  cout << "--- 평범한 Child 만들었을 때 ---\n";
  { Child c; }
  cout << "--- Parent 포인터로 Child 가리켰을 때 ---\n";
  {
    Parent *p = new Child();
    delete p;
  }
}
```

```
--- 평범한 Child 만들었을 때 ---
Parent 생성자 호출
Child 생성자 호출
Child 소멸자 호출
Parent 소멸자 호출
--- Parent 포인터로 Child 가리켰을 때 ---
Parent 생성자 호출
Child 생성자 호출
Child 소멸자 호출
Parent 소멸자 호출
```

왜 Parent 소멸자는 호출이 되었는가?  
이는 Child 소멸자를 호출하면서, Child 소멸자가 **알아서** Parent 의 소멸자도 호출해주기 때문  
(Child 는 자신이 Parent 를 상속받는다는 것을 알고 있음)

반면에 Parent 소멸자를 먼저 호출하게 되면, Parent 는 Child 가 있는지 없는지 모르므로,  
Child 소멸자를 호출해줄 수 없음  
(Parent 는 자신이 누구에서 상속해주는지 알 수 없음)

이와 같은 이유로, 상속될 여지가 있는 Parent 클래스들은 반드시 소멸자를 virtual 로 만들어주어야  
나중에 문제가 발생할 여지가 없게 됨

## 가상 함수의 구현 원리  
왜 C++ 에서는 virtual 키워드를 이용해 사용자가 직접 virtual 로 선언하도록 하였을까  
그 이유는 가상 함수를 사용하게 되면 약간의 **오버헤드 (overhead)** 가 존재하기 때문  

C++ 컴파일러는 가상 함수가 하나라도 존재하는 클래스에 대해서, **가상 함수 테이블(virtual function table; vtable)** 을 만들게 됨  
함수의 이름과 실제로 어떤 함수가 대응되는지 테이블로 저장하고 있는 것  

비 가상함수들은 그냥 단순히 특별한 단계를 걸치지 않고 함수를 호출하면 직접 실행됨  

하지만, 가상 함수를 호출하였을 때는 가상 함수 테이블을 한 단계 더 걸쳐서, 실제로 어떤 함수를 고를지 결정하게 됨  

따라서 보통의 함수를 호출하는 것 보다 가상 함수를 호출하는 데 걸리는 시간이 조금 더 오래 걸림  

## 순수 가상 함수(pure virtual function)와 추상 클래스(abstract class)
```
class Animal {
 public:
  Animal() {}
  virtual ~Animal() {}
  virtual void speak() = 0;
};
```
speak 함수는 함수의 몸통이 정의되어 있지 않고 단순히 = 0; 으로 처리되어 있는 가상 함수  
이 함수는 "무엇을 하는지 정의되어 있지 않는 함수"  
다시 말해 이 함수는 **반드시 오버라이딩 되어야만 하는 함수**  

이렇게, 가상 함수에 = 0; 을 붙여서, 반드시 오버라이딩 되도록 만든 함수를  
완전한 가상 함수라 해서, **순수 가상 함수(pure virtual function)** 라고 부름  

순수 가상 함수는 본체가 없기 때문에, 이 함수를 호출하는 것은 불가능  
그렇기 때문에, Animal 객체를 생성하는것 또한 불가능  

따라서 Animal 처럼, 순수 가상 함수를 최소 한 개 이상 포함하고 있는 클래스는 객체를 생성할 수 없으며,  
인스턴스화 시키기 위해서는 이 클래스를 상속 받는 클래스를 만들어서 모든 순수 가상 함수를 오버라이딩 해주어야만 함

이렇게 순수 가상 함수를 최소 한개 포함하고 있는, 반드시 상속 되어야 하는 클래스를 가리켜  
**추상 클래스 (abstract class)** 라고 부름

추상 클래스는 이 클래스를 상속받아서 사용하는 사람에게 "이 기능은 일반적인 상황에서 만들기 힘드니 너가 직접 특수화 되는 클래스에 맞추어서 만들어서 써라." 라고 말해주는 것  

추상 클래스의 또 한가지 특징은 비록 객체는 생성할 수 없지만, 추상 클래스를 가리키는 포인터는 문제 없이 만들 수 있다는 점  