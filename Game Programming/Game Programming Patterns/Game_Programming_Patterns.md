# 게임 프로그래밍 패턴

지금까지 간단하게 만든 게임들은 기능 구현에만 목표를 두고 제작하여 나중에 다시 코드를 봤을 때 이해하기가 쉽지 않았음  
따라서 게임 프로그래밍 패턴을 공부하며 기능 구현에만 초점을 두는 것이 아닌, 나중에 미래의 나 혹은 다른 팀원이 보고 빠르게 이해하면서 기능 추가, 수정에 용이한 구조로 코드를 짜는 방법을 익히려고 함

---

## 목차
1. [도입](#Part-1.-도입)
    - [구조, 성능, 게임](#Chapter-1.-구조,-성능,-게임)
2. [디자인 패턴 다시 보기](#Part-2.-디자인-패턴-다시-보기)
    - [명령](#CHAPTER-2.-명령)
    - 경량
    - 관찰자
    - 프로토타입
    - 싱글턴
    - 상태
3. 순서 패턴
    - 이중 버퍼
    - 게임 루프
    - 업데이트 메서드
4. 행동 패턴
    - 바이트코드
    - 하위 클래스 샌드박스
    - 타입 객체
5. 디커플링 패턴
    - 컴포넌트
    - 이벤트 큐
    - 서비스 중개자
6. 최적화 패턴
    - 데이터 지역성
    - 더티 플래그
    - 객체 풀
    - 공간 분할

---

---
## Part 1. 도입  
---
### Chapter 1. 구조, 성능, 게임  
#### 좋은 소프트웨어 구조란?  
뭔가를 고쳐야 할 때 '그럴 줄 알았다'는 듯이 코드가 준비되어 있는 것을 의미  
즉, 코드를 거의 건드리지 않고도 적당한 함수 몇 개만 호출하면 원하는 작업을 할 수 있어야함  
_(얼마나 쉽게 변경할 수 있는가가 코드 설계를 평가하는 척도가 됨)_

#### 디커플링  
양쪽 코드 중에서 한쪽이 없으면 코드를 이해할 수 없을 때 둘이 **디커플링** 되어있다고 함  
따라서 두 코드를 **디커플링**하면, 각각의 코드를 따로 이해할 수 있게 됨

#### 비용  
많은 개발자들은 구조에 제약이 없고, 강력하며, 확장하기 쉬운 코드베이스를 만들고 싶어한다.  
하지만, 이를 위해 코드를 추가하고, 복잡성을 늘리다 보면 개발, 디버깅, 유지보수에 시간이 더 걸리게 된다.  
예측이 맞아서 코드를 수정할 일이 생긴다면 이러한 작업이 헛수고가 되지 않지만, 예측이 빗나간다면 안 만드느니만 못하는 코드가 되어버린다.

#### 성능과 속도  
소프트웨어 구조는 코드를 더 유연하고 쉽게 변경할 수 있게 만들기 위해 존재한다. 이는 프로그램에서 가정을 줄인다는 뜻이다.  
성능은 전부 가정에 기반하고, 최적화 기법은 구체적인 제한을 선호한다.  
_(모든 개체가 같은 클래스라고 확신한다면 배열에 전부 넣을 수 있다. 등)_  
개발 속도는 게임 재미를 찾는데 꼭 필요하다. 기획서만으로는 게임 밸런스를 맞출수 없기 때문에 반복 개발과 실험을 같이 해야한다.  
프로토타이핑을 빠르게 하기 위해서 프로그램을 유연하게 만들면 성능상 비용이 발생하고, 반대로 코드를 최적화하면 유연성이 떨어진다.  

#### 나쁜 코드의 장점  
구조화가 잘된 코드를 작성하려면 많은 고민, 즉 많은 시간이 필요하다.    
기획 확인에 필요한 코드는 간신히 돌아가도록 대강 코드를 작성해 프로토타입을 보여주고 피드백을 한 뒤, 기획이 결정되면 그 때 코드를 버리거나, 다시 구조화가 잘된 코드로 만들면 된다.  
_(단, 버릴 코드는 확실히 버리고 필요하다면 구조화하여 다시 만들어야한다.)_

#### 균형 잡기  
1. 프로젝트 개발 기간 동안 코드를 쉽게 이해할 수 있도록 구조를 깔끔하게 만들고 싶다.
2. 실행 성능을 최적화하고 싶다.
3. 지금 개발 중인 기능을 최대한 빠르게 구현하고 싶다.

이 목표들은 서로 어느 정도 상반되기 때문에, 그 사이에서 **균형**을 잘 잡아야한다.

#### 단순함

위의 제약들을 완화할 방법은 **단순함**이다.  
코드를 최대한 간결하게, 문제를 직접 해결하는 방향으로 짜면 코드를 읽어보면 의도를 바로 알 수 있다.  
코드를 고칠 때 좋은 해결책은 코드를 덧붙이는게 아니라, **필요 없는 코드를 최대한 빼는 것**이다.

#### 마치며
- 추상화와 디커플링을 잘 활용하면 코드를 점차 쉽고 빠르게 만들 수 있지만,   
지금 고민 중인 코드에 유연함이 필요하다는 확신이 없으면 추상화와 디커플링을 적용하느라고 시간 낭비하면 안됨
- 개발 내내 성능을 고민하고, 최적화에 맞게 설계해야 함,  
다만 가정을 코드에 박아 넣어야하는 저수준의 핵심 최적화는 가능하면 늦게 해야함
- 게임 기획 내용을 확인해볼 수 있도록 빠르게 개발하되, 너무 서두르느라 코드를 엉망으로 만들면 안됨.  
결국 그 코드로 내가 다시 작업해야함
- 나중에 버릴 코드를 잘 만들겠다고 시간 낭비 하지 말 것
---
---
## Part 2. 디자인 패턴 다시 보기  
---
### CHAPTER 2. 명령  
**명령** 패턴은 메서드 호출을 실체화한 것  
실체화 : 어떤 개념을 변수에 저장하거나 함수에 전달할 수 있도록 **데이터**, 즉 **객체**로 바꿀수 있다는 것을 의미 _(콜백, 함수 포인터와 비슷)_  

#### 명령 패턴 예제 _(입력 키 변경)_
```
void InputHandler::handleInput(){
    if(isPressed(BUTTON_X)) jump();
    else if(isPressed(BUTTON_Y)) fireGun();
    else if(isPressed(BUTTON_A)) swapWeapon();
    else if(isPressed(BUTTON_B)) lurchIneffectively();
}
```

![command-buttons-one](https://user-images.githubusercontent.com/32252062/66831447-9ffe0800-ef92-11e9-8462-538e95a37ead.png)

이러한 코드에서 키 변경을 지원하려면 jump()나 fireGun() 같은 **함수를 직접 호출하지 말고 교체 가능한 무엇인가로 바꿔야 함**  
→ **객체**가 있어서 이를 변수에 할당

게임에서 할 수 있는 행동을 실행할 수 있는 공통 상위 클래스 정의
```
class Command{
public:
    virtual ~Command() {}
    virtual void execute() = 0;
}
```

각 행동별로 하위 클래스 생성
```
class JumpCommand : public Command{
public:
    virtual void execute() { jump(); }
}

class FireCommand : public Command{
public:
    virtual void execute() { fireGun(); }
}

...
```

입력 핸들러 코드에는 각 버튼별로 Command 클래스 포인터를 저장
```
class InputHandler {
public:
    void handleInput();
    // 명령을 바인드(bind)할 메서드들
private:
    Command* buttonX_;
    Command* buttonY_;
    Command* buttonA_;
    Command* buttonB_;
}
```

입력 처리는 다음과 같이 변경
```
void InputHandler::handleInput(){
    if(isPressed(BUTTON_X)) buttonX_->execute();
    else if(isPressed(BUTTON_Y)) buttonY_->execute();
    else if(isPressed(BUTTON_A)) buttonA_->execute();
    else if(isPressed(BUTTON_B)) buttonB_->execute();
}
```
![command-buttons-two](https://user-images.githubusercontent.com/32252062/66831461-aa200680-ef92-11e9-8a67-df94a74dfefc.png)

이런 식의 구조가 되면 직접 함수를 호출하던 코드 대신에, **한 겹 우회하는 계층**이 생기게 됨

#### 액터에게 지시하기
위에서 정의한 Command 클래스는 이번 예제만 놓고 보면 잘 동작하지만 한계가 있음  
_(jump()와 같은 저녁 함수가 플레이어 캐릭터 객체를 암시적으로 찾아서 움직이게 할 수 있다던가 하는 것)_
현재 JumpCommand 클래스는 오직 플레이어 캐릭터만 점프하게 만들 수 있음  
이런 제약을 유연하게 만들기 위해 제어하려는 객체를 함수 밖에서 전달
```
class Command{
public:
    virtual ~Command() {}
    virtual void execute(GameActor& actor) = 0;
}

class JumpCommand : public Command{
public:
    virtual void execute(GameActor& actor) { actor.jump(); }
}
```

이런 식으로 작성하면 클래스 하나로 게임에 등장하는 어떤 객체도 점프시킬 수 있음  
남은 것은 입력 핸들러에서 입력을 받아 적당한 객체의 메서드를 호출하는 명령 객체를 연결하는 것  
```
void InputHandler::handleInput(){
    if(isPressed(BUTTON_X)) return buttonX_;
    if(isPressed(BUTTON_Y)) return buttonY_;
    if(isPressed(BUTTON_A)) return buttonA_;
    if(isPressed(BUTTON_B)) return buttonB_;

     // 아무것도 누르지 않았다면, 아무것도 하지 않음
    return NULL;
}
```
어떤 액터를 매개변수로 넘겨줘야 할지 모르기 때문에 handleInput()에서는 명령을 실행할 수 없음  
_(명령이 실체화된 함수 호출이라는 점을 활용 → 함수 호출 시점 지연)_

명령 객체를 받아서 플레이어를 대표하는 객체에 적용하는 코드가 필요
```
Command* command = inputHandler.handleInput();
if(command) { command->execute(actor); }
```

명령과 액터 사이에 **추상 계층을 한 단계 더 둔 덕분에**, 명령을 실행할 때 액터만 바꾸면 플레이어가 게임에 있는 어떤 액터라도 제어 할 수 있게 됨

#### 실행취소와 재실행
명령 객체가 어떤 작업을 **실행**할 수 있다면, 이를 **실행취소**할 수 있게 만드는 것도 어렵지 않음  
턴제 게임에서 유닛을 옮기는 명령
```
class MoveUnitCommand : public Command{
public:
    MoveUnitCommand(Unit* unit, int x, int y) : unit_(unit), x_(x), y_(y) {}

    virtual void execute() { unit_->moveTo(x_, y_); }

private:
    Unit* unit_;
    int x_;
    int y_;
}
```

이전 예제에서는 명령에서 변경하려는 액터와 명령 사이를 **추상화로 격리**  
→ **어떤 일을 하는지**를 정의한 명령 객체 하나가 매번 재사용  
→ 입력 핸들러 코드에서 특정 버튼이 눌릴 때마다 여기에 **연결된 명령 객체의 execute()를 호출**

이번에는 이동하려는 유닛과 위치 값을 생성자에서 받아서 명령과 **명시적으로 바인드**  
→ 특정 시점에 발생될 일을 표현, 좀 더 구체적  
→ 입력 핸들러 코드에서 플레이어가 이동을 선택할 때마다 **명령 인스턴스를 생성**

```
Command* handleInput(){
    Unit* unit = getSelectedUnit();
    if(isPresssed(BUTTON_UP)){
        int dsetY = unit->y() - 1;
        return new MoveUnitCommand(unit, unit->x(), dsetY);
    }
    if(isPresssed(BUTTON_DOWN)){
        int dsetY = unit->y() + 1;
        return new MoveUnitCommand(unit, unit->x(), dsetY);
    }

    // 다른 이동들
    ...

    return NULL;
}
```

명령을 취소할 수 있도록 undo()를 정의
```
class Command{
public:
    virtual ~Command() {}
    virtual void execute() = 0;
    virtual void undo() = 0;
}
```

undo()는 execute()에서 변경하는 게임 상태를 반대로 바꿔주면 됨
```
class MoveUnitCommand : public Command{
public:
    MoveUnitCommand(Unit* unit, int x, int y) : unit_(unit), x_(x), y_(y)
    xBefore_(0), yBefore_(0), x_(x), y_(y) {}

    virtual void execute() {
        // 나중에 이동을 취소할 수 있도록 원래 유닛 위치를 저장
        xBefore_ = unit->x();
        yBefore_ = unit->y();
        unit_->moveTo(x_, y_); 
    }
    
    virtual void undo() { unit_->moveTo(xBefore_, yBefore); }

private:
    Unit* unit_;
    int x_, y_;
    int xBefore_, yBefore_;
}
```
![command-undo](https://user-images.githubusercontent.com/32252062/66834139-105b5800-ef98-11e9-9be6-4f172dea61d9.png)

여러 단계의 실행취소를 지원하는 것도 어렵지 않음  
가장 최근 명령만 기억하는 대신, 명령 목록을 유지하고 ***현재***  명령이 무엇인지만 알고 있으면 됨  
유저가 명령을 실행하면, 새로 생성된 명령을 목록 맨 뒤에 추가하고, 이를 ***현재*** 명령으로 기억하면 됨  
유저가 ***실행취소*** 를 선택하면 현재 명령을 실행취소하고 현재 명령을 가리키는 포인터를 뒤로 이동  
유저가 ***재실행*** 을 선택하면, 포인터를 다음으로 이동시킨 후에 해당 포인터를 실행

---
> 본 내용은 한빛미디어에서 출판한 '게임 프로그래밍 패턴 : 더 빠르고 깔끔한 게임 코드를 구현하는 13가지 디자인 패턴' 을 읽고 공부하며 작성하였습니다.