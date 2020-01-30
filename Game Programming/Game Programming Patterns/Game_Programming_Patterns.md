# 게임 프로그래밍 패턴

지금까지 간단하게 만든 게임들은 기능 구현에만 목표를 두고 제작하여 나중에 다시 코드를 봤을 때 이해하기가 쉽지 않았음  
따라서 게임 프로그래밍 패턴을 공부하며 기능 구현에만 초점을 두는 것이 아닌, 나중에 미래의 나 혹은 다른 팀원이 보고 빠르게 이해하면서 기능 추가, 수정에 용이한 구조로 코드를 짜는 방법을 익히려고 함

---

## 목차
1. [도입](#Part-1.-도입)
    - [구조, 성능, 게임](#Chapter-1.-구조,-성능,-게임)
2. [디자인 패턴 다시 보기](#Part-2.-디자인-패턴-다시-보기)
    - [명령](#CHAPTER-2.-명령)
    - [경량](#CHAPTER-3.-경량)
    - [관찰자](#CHAPTER-4.-관찰자)
    - [프로토타입](#CHAPTER-5.-프로토타입)
    - [싱글턴](#CHAPTER-6.-싱글턴)
    - [상태](#CHAPTER-7.-상태)
3. [순서 패턴](#Part-3.-순서-패턴)
    - [이중 버퍼](#CHAPTER-8.-이중-버퍼)
    - [게임 루프](#CHAPTER-9.-게임-루프)
    - [업데이트 메서드](#CHAPTER-10.-업데이트-메서드)
4. [행동 패턴](#Part-4.-행동-패턴)
    - [바이트코드](#CHAPTER-11.-바이트코드)
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
---
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
---
### CHAPTER 3. 경량  
---
실시간 게임으로 나무들이 화면을 가득 채운 빽빽한 숲을 구현하는 것은 단순한 문장의 표현과 다른 이야기  
수천 그루가 넘는 나무마다 각각 수천 폴리곤의 형태로 표현해야 하는데, 설사 메모리가 충분하다고 해도, 이런 숲을 그리기 위해서는 전체 데이터를 CPU에서 GPU로 버스를 통해 전달해야 함  

나무마다 필요한 데이터는 다음과 같음
 - 줄기, 가지, 잎의 형태를 나타내는 폴리곤 메시
 - 나무 껍질과 잎사귀 텍스처
 - 숲에서의 위치와 방향
 - 각각의 나무가 다르게 보이도록 크기와 음영 같은 값을 조절할 수 있는 매개변수  

이를 코드로 표현하면 다음과 같음
```
class Tree{
private:
    Mesh mesh_;
    Texture bark_;
    Texture leaves_;
    Vector position_;
    double height_;
    double thickness_;
    Color barkTint_;
    Colot leafTint_;
};
```
![flyweight_01](https://user-images.githubusercontent.com/32252062/69784932-5c85f300-11fa-11ea-8179-84aa43779bca.png)
(그림 3-1) 작은 상자에 들어 있는 내용은 모든 나무가 동일

이렇게 많은 객체로 이루어진 숲 전체는 1프레임에 GPU로 모두 전달하기에는 양이 너무 많음  
다행히도 검증된 해결책이 있음  

핵심은 숲에 나무가 수천 그루 넘게 있다고 해도 대부분 비슷해 보인다는 점  
그렇다면 모든 나무를 같은 메시와 텍스처로 표현할 수 있을 것. 즉, 나무 객체에 들어 있는 데이터 대부분이 인스턴스별로 다르지 않다는 뜻  

객체를 반으로 쪼개어 이런 점을 명시적으로 모델링 할 수 있음  
```
class TreeModel{
private:
    Mesh mesh_;
    Texture bark_;
    Texture leaves_;
};
```

게임 내에서 여러 번 메모리에 올릴 이유가 전혀 없기 때문에 TreeModel 객체는 하나만 존재  
나무 인스턴스는 공유 객체인 TreeModel을 **참조**하기만 함  
Tree 클래스에는 인스턴스별로 다른 상태 값만 남겨 둠  
```
class Tree{
private:
    TreeModel* model_;

    Vector position_;
    double height_;
    double thickness_;
    Color barkTint_;
    Colot leafTint_;
};
```
![flyweight_02](https://user-images.githubusercontent.com/32252062/69785815-66a8f100-11fc-11ea-9c49-3c6dfcaea75c.png)
(그림 3-2) 나무 인스턴스 4개가 모델 하나를 공유

#### 수천 개의 인스턴스
GPU로 보내는 데이터 양을 최소화하기 위해서는 공유 데이터인 TreeModel을 딱 한 번만 보낼 수 있어야함  
나무마다 값이 다른 위치, 색, 크기를 전달하고, 마지막에 GPU에 전체 나무 인스턴스를 그릴 때 공유 데이터를 사용하라고 하면 됨  

이런 기능을 **인스턴스 렌더링**이라고 함  
인스턴스 렌더링을 하려면 데이터 스트림이 두 개 필요함  
첫 번째 스트림에는 여러 번 렌더링되어야 하는 공유 데이터가 들어가고,  
두 번째 스트림에는 인스턴스 목록과 이들 인스턴스를 첫 번째 스트림 데이터를 이용해 그릴 때 각기 다르게 보이기 위해 필요한 매개변수들이 들어감  

#### 경량 패턴
경량 패턴은 _어떤 객체의 개수가 너무 많아서 좀 더 가볍게 만들고 싶을 때 사용_

이를 위해서 객체 데이터를 두 종류로 나누는데, 먼저 모든 객체의 데이터 값이 같아서 공유할 수 있는 데이터를 모음_(고유 상태, 자유문맥)_, 예제에서는 나무 형태나 텍스처가 해당  
나머지 데이터는 인스턴스별로 값이 다른 _외부상태_에 해당, 예제에서는 나무의 위치, 크기, 색 등이 이에 해당함

공유 객체가 명확하지 않은 경우 경량 패턴은 잘 드러나 보이지 않음  
그런 경우에는 하나의 객체가 신기하게도 여러 곳에 동시에 존재하는 것처럼 보임

#### 지형 정보 예제
타일 기반의 지형을 만드는데 지형 종류에는 게임플레이에 영향을 주는 여러 속성이 들어있음  
 - 플레이어가 얼마나 빠르게 이동할 수 있는지를 결정하는 이동 비용 값
 - 강이나 바다처럼 보트로 건너갈 수 있는 곳인지 여부
 - 렌더링할 때 사용할 텍스처  

```
enum Terrain {
    TERRAIN_GRASS,
    TERRAIN_HILL,
    TERRAIN_RIVER
    // 그 외 다른 지형들...
};
```

월드는 지형을 거대한 격자로 관리
```
class world{
private:
    Trrain tiles_[WIDTH][HEIGHT];
};
```

타일 관련 데이터는 다음과 같이 얻을 수 있음
```
int World::getMovementConst(int x, int y){
    switch(tiles_[x][y]){
        case TERRAIN_GRASS: return 1;
        case TERRAIN_HILL: return 3;
        case TERRAIN_RIVER: return 2;
        // 그 외 다른 지형들...
    }
}

bool World::isWater(int x, int y){
    switch(tiles_[x][y]){
        case TERRAIN_GRASS: return false;
        case TERRAIN_HILL: return false;
        case TERRAIN_RIVER: return true;
        // 그 외 다른 지형들...
    }
}
```

이 코드는 동작하긴 하지만 지저분함  
이동 비용이나 물인지 땅인지 여부는 지형에 관한 데이터인데 이 코드에서는 하드코딩 되어있음  
게다가 같은 지형 종류에 대한 데이터가 여러 메서드에 나뉘어 있음  
이런 데이터는 하나로 합쳐서 캡슐화하는게 좋음  

아래와 같이 지형 **클래스**를 따로 만드는 게 훨씬 나음
```
class Terrain{
public:
    Terrain(int movementCost, bool isWater, Texture texture)
    : movementCost_(movementCost), 
      isWater_(isWater), 
      texture_(texture){
    }

    int getMovementCost() const { return movementCost_; }
    bool isWater() const { return isWater_; }
    const Texture& getTexture() const { return texture_; }
    
private:
    int movementConst_;
    bool isWater_;
    Texture texture_;
};
```
모든 메서드를 const로 만든 이유는 같은 Terrain 객체를 여러 곳에서 공유해서 쓰기 때문에, 한곳에서 값을 바꾼다면 그 결과가 여러 군데에서 동시에 나타나게 됨  
메모리를 줄여보겠다고 객체를 공유했는데 그게 코드 기능에 영향을 미쳐서는 안됨  
이런 이유로 경량 객체는 변경 불가능한(immutable) 상태로 만드는게 보통  

Terrain 클래스에는 타일 위치와 관련된 내용은 전혀 없는 데, 경량 패턴식으로 얘기하자면 모든 지형상태는 _고유_함. 즉, _자유문맥_에 해당됨  
따라서 지형 종류별로 Terrain 객체가 여러 개 있을 필요가 없음  
즉, World 클래스 격자 멤버 변수에 열거형이나 Terrain 객체 대신 Terrain 객체의 포인터를 넣을 수 있음

```
class world{
private:
    Trrain* tiles_[WIDTH][HEIGHT];
    // 그외...
};
```
![flyweight_03](https://user-images.githubusercontent.com/32252062/69792607-4aac4c00-120a-11ea-81ee-b51382b1fc9a.png)
(그림 3-3) Terrain 객체를 재사용하는 타일들

Terrain 인스턴스가 여러 곳에서 사용되다 보니, 동적으로 할당하면 생명주기를 관리하기가 좀 더 어려움  
따라서 World 클래스에 저장
```
class world{
public:
    World()
    : grassTerrain_(1, false, GRASS_TEXTURE), 
      hillTerrain_(3, false, HILL_TEXTURE), 
      riverTerrain_(2, true, RIVER_TEXTURE){
    }
    
private:
    Terrain grassTerrain_;
    Terrain hillTerrain_;
    Terrain riverTerrain_;
    // 그 외...
};
```

이렇게 함으로써 다음과 같이 땅 위를 채울 수 있음
```
void World::generateTerrain(){
    // 땅에 풀을 채움
    for(int x = 0; x < WIDTH; x++){
        for(int y = 0; y < HEIGHT; y++){
            // 언덕을 몇 개 놓음
            if(random(10) == 0){
                tiles_[x][y] = &hillTerrain_;
            }
            else {
                tiles_[x][y] = &grassTerrain_;
            }
        }
    }

    // 강을 하나 놓음
    int x = random(WIDTH);
    for(int y = 0; y < HEIGHT; y++){
        tiles_[x][y] = &riverTerrain_;
    }
}
```

이제 지형 속성 값을 World의 메서드 대신 Terrain 객체에서 바로 얻을 수 있음
```
const Terrain& World::getTile(int x, int y) const{
    return *tiles_[x][y];
}
```
World 클래스는 더 이상 지형의 세부 정보와 커플링 되지 않음  

#### 성능에 대해서
위와 같이 포인터로 접근한다는 것은 간접 조회 한다는 뜻  
이동 비용 같은 지형 데이터 값을 얻으려면 먼저 격자 데이터로부터 지형 객체 포인터를 얻은 다음에, 포인터를 통해서 이동 비용 값을 얻어야함  
이렇게 포인터를 따라가면 캐시 미스가 발생할 수 있어 성능이 조금 떨어질 수는 있음  

객체가 메모리에 어떤 식으로 배치되느냐에 따라 열거형과 포인터의 성능이 달라질 수 있지만,  
경량 객체를 한 번은 고려해보는 것이 좋음  
경량 패턴을 사용하면 객체를 마구 늘리지 않으면서도 객체지향 방식의 장점을 취할 수 있음  
열거형을 선언해 수많은 switch를 만들 생각이라면, 경량 패턴을 먼저 고려

---
---
### CHAPTER 4. 관찰자  
---
> 객체 사이에 일 대 다의 의존 관계를 정의해두어, 어떤 객체의 상태가 변할 때 그 객체에 의존성을 가진 다른 객체들이 그 변화를 통지 받고 자동으로 업데이트될 수 있게 만듬

#### 4.1 업적 달성
업적은 종류가 광범위하고 달성할 수 있는 방법도 다양하다 보니 깔끔하게 구현하기 어려움  

특정 기능을 담당하는 코드는 한데 모아두는게 좋음  
문제는 업적을 여러 게임 플레이 요소에서 발생시킬 수 있다는 점  
코드 전부와 커플링되지 않고도 업적 코드가 동작하게 하려면?  
**관찰자 패턴**을 사용하면 됨  

관찰자 패턴을 사용하면 어떤 코드에서 **흥미로운 일**이 생겼을 때 **누가 받는 상관없이** 알림을 보낼 수 있음

'다리에서 떨어지기' 업적을 구현하기 위해 업적 코드를 물리 코드에 넣을 수 있지만, 그러면 코드가 지저분해짐  
대신에 다음과 같은 방법을 사용
```
void Physics::updateEntity(Entity& entity){
    bool wasOnSurface = entity.isOnSurface();
    entity.accelerate(GRAVITY);
    entity.update();
    if(wasOnSurface && !entity.isOnSurface()){
        notify(entity, EVENT_START_FALL)
    }
}
```
이 코드는 'entity가 떨어지기 시작했으니 누군가 알아서 해주세요' 라고 알리는 게 전부  
업적 시스템은 물리 엔진이 알림을 보낼 때마다 받을 수 있도록 스스로를 등록함  
업적 시스템은 떨어지는 물체가 캐릭터가 맞는지, 그리고 떨어지기 전에 다리 위에 있었는지를 확인한 뒤 업적을 잠금해제함  
이러한 과정은 물리 코드를 전혀 몰라도 됨

#### 4.2 작동 원리
 - 관찰자
```
class Observer {
    public : virtual ~Observer() {}
    virtual void onNotify(const Entity& entity, Event event) = 0;
}
```
onNotify()에 어떤 매개변수를 넣을지는 알아서 하면 됨  
보통은 알림을 보내는 객체와 다른 구체적인 정보를 담은 일반적인 '데이터'를 매개변수로 넘김  
아래의 예제에서는 하드코딩 하였지만, **필요에 맞게 원하는 자료형**을 전달해도 됨

어떤 클래스든 Observer 인터페이스를 구현하기만 하면 관찰자가 될 수 있음
```
class Achievements : public Observer{
public:
    virtual void onNotify(const Entity& entity, Event event){
        switch(event){
        case EVENT_ENTITY_FELL:
            if(entity.isHero() && heroIsOnBridge_){
                unlock(ACHIEVEMENT_FELL_OFF_BRIDGE);
            }
            break;
            // 그 외 다른 이벤트를 처리
            // heroIsOnBridge_ 값을 업데이트
        }
    }

private:
    void unlock(Achievement achievment){
        // 업적이 잠겨 있다면 잠금해제
    }
    bool heroIsOnBridge_;
};
```

 - 대상
알림 메서드는 **관찰당하는 객체**가 호출함, 이런 객체를 **대상(subject)** 이라고 부름  
대상이 수행하는 일 중 하나는 알림을 기다리는 관찰자 목록을 가지고 있는 것  
```
class Subject{
public:
    void addObserver(Observer* observer){
        // 배열에 추가
    }
    void removeObserver(Observer* observer){
        // 배열에서 제거
    }
private:
    Observer* observers_[MAX_OBSERVERS];
    int numObservers_;
};
```

여기서 중요한 것은 **관찰자 목록을 밖에서 변경할 수 있도록 public으로** 열어놓은 점  

이를 통해 누가 알림을 받을 것인지를 제어할 수 있음  
대상은 관찰자와 상호작용하지만, 서로 커플링되어있지 않음, 이것이 관찰자 패턴의 장점  

대상이 관찰자를 여러 개 등록할 수 있게 하면 관찰자들이 각자 독립적으로 다뤄지는 걸 보장할 수 있음  
```
class Subject{
protected:
    void notify(const Entity& entity, Event event){
        for(int i = 0; i < numObservers_; i++){
            observers_[i]->onNotify(entity, event);
        }
    }
    // 그 외
};
```

 - 물리 관찰
남은 작업은 물리 엔진에 훅을 걸어 알림을 보낼 수 있게 하는 일과 업적 시스템에서 알림을 받을 수 있도록 스스로를 등록하게 하는 일  
```
class Physics : public Subject{
public:
    void updateEntity(Entity& entity);
};
```

이렇게 하면 Subject 클래스의 notify() 메서드를 protected로 만들 수 있음  
Subject를 상속받은 Physics 클래스는 notify()를 통해서 알림을 보낼 수 있지만, 밖에서는 notify()에 접근할 수 없음  
반면, addObserver()와 removeObserver()는 public이기 때문에 물리 시스템에 접근할 수만 있다면 물리 시스템을 관찰할 수 있음  

![Observer_01](https://user-images.githubusercontent.com/32252062/69865748-70f2ea00-12e5-11ea-9f40-37c03e2b9021.png)
(그림 4-2) 대상과 대상에서 관리하고 있는 관찰자 레퍼런스 목록

#### 4.3 속도
관찰자 패턴을 제대로 이해하지 못한다면 CPU를 낭비할 것으로 보일 수 있음  
관찰자 패턴은 특히 '이벤트', '메시지', '데이터 바인딩' 등과 주로 사용되는데, 이런 시스템 중 일부는 알림이 있을 때마다 동적 할당 하거나 큐잉하기 때문에 실제로 느릴 수 있음  

하지만 관찰자 패턴은 전혀 느리지 않음  
정적 호출보다는 약간 느리지만, 진짜 성능에 민감한 코드가 아니라면 문제가 되지 않음  
게다가 관찰자 패턴은 성능에 민감하지 않은 곳에 가장 잘 맞기 때문에, 동적 디스패치를 써도 크게 상관없음  

느린 것보다 주의해야 할 점은 관찰자 패턴이 동기적이라는 점임  
대상이 관찰자 메서드를 직접 호출하기 때문에 모든 관찰자가 알림 메서드를 반환하기 전에는 다음 작업을 진행할 수 없음  
관찰자 중 하나라도 느리면 대상이 블록될 수 있음  

이벤트에 동기적으로 반응한다면 최대한 빨리 작업을 끝내고 제어권을 다시 넘겨줘서 UI가 멈추지 않게 해야함  
오래 걸리는 작업은 다른 스레드로 넘기거나 작업 큐를 활용해야함  

관찰자를 **멀티스레드, 락(lock)과 함께 사용할 때는 정말 조심**해야함  
어떤 관찰자가 **대상의 락을 물고 있다면 게임 전체가 교착상태**에 빠질 수 있기 때문  
엔진에서 멀티스레드를 많이 쓰고 있다면, **이벤트 큐**를 이용해 **비동기적**으로 상호작용하는 게 더 좋을 수 있음

#### 4.4 동적 할당
예제에서는 고정 배열을 사용하였으나, 실제 게임 코드라면 관찰자가 추가, 삭제 될 때 동적 할당을 사용했을 것  
물론 실제로는 관찰자가 추가될 때만 메모리를 할당하고, 알림을 보낼 때는 메서드를 호출할 뿐 동적 할당은 전혀 하지 않음  
따라서 게임 코드가 실행될 때 처음 관찰자를 등록해놓은 뒤에 건드리지 않는다면 메모리 할당은 거의 일어나지 않음  

 - 관찰자 연결리스트  
 
 ![image](https://user-images.githubusercontent.com/32252062/70036722-ad318d80-15f8-11ea-8369-af95e8ae2de4.png)
 (그림 4-3) 대상은 관찰자 연결 리스트를 포인터로 가리킴

먼저 Subject 클래스에 배열 대신 관찰자 연결 리스트의 첫째 노드를 가리키는 포인터를 둠
```
class Subject{
    Subject() : head_(NULL) {}

    // 메서드들
private:
    Observer* head_;
};
```

이후 Observer에 연결 리스트의 다음 관찰자를 가리키는 포인터를 추가
```
class Observer{
    friend class Subject;

public:
    Observer() : next_(NULL) {}

    // 그 외
private:
    Observer* next_;
};
```
Subject를 friend 클래스로 정의  
Subject에는 관찰자를 추가, 삭제하기 위한 API가 있지만 Subject가 관리해야 할 관찰자 목록은 Observer 클래스 안에 있음  
Subject가 이들 목록에 접근할 수 있게 만드는 가장 간단한 방법은 Observer에서 Subject를 friend클래스로 만드는 것  
(friend로 하면 Subject에서 Observer의 private 영역까지 접근 가능)  

새로운 관찰자를 연결 리스트에 추가하기만 하면 대상에 등록할 수 있음  
```
void Subject::addObserver(Observer* observer){
    observer->next_ = head_;
    head_ = observer;
}
```

연결 리스트를 뒤쪽으로 추가하면 관찰자를 추가할 때마다 리스트에서 마지막 노드를 찾거나 마지막 노드를 따로 tail_ 포인터로 관리해야하기 때문에 좀 더 복잡해짐  

관찰자를 앞에서 추가하면 구현이 간단하나, 전체 관찰자에 알림을 보낼 때는 **맨 나중에** 추가된 관찰자부터 **맨 먼저** 알림을 받는다는 부작용이 있음  
원칙적으로는 같은 대상을 관찰하는 관찰자끼리는 알림 순서로 인한 의존 관계가 없게 만들어야함  
순서 때문에 문제가 있다면 관찰자들 사이에 커플링이 있다는 이야기이고, 이는 나중에 문제가 될 수 있음  

```
void Subject::removeObserver(Observer* observer){
    if(head_ == observer){
        head_ = observer->next_;
        observer->next_ = NULL;
        return;
    }

    Observer* current = head_;
    while(current != NULL){
        if(current->next_) == observer){
            current->next_ = observer->next_;
            observer->next_ = NULL;
            return;
        }
        current = current->next_;
    }
}
```
노드를 제거하려면 연결 리스트를 순회해야함(단순 연결 리스트이기 때문에)  
만약에 실제 코드였다면 상수 시간에 제거가 가능한 이중 연결 리스트를 사용할 것  

```
void Subject::notify(const Entity& entity, Event event){
    Observer* observer = head_;
    while(observer != NULL){
        observer->onNotify(entity, event);
        observer = observer->next_;
    }
}
```
알림을 보내는 것은 목록을 따라가면 됨  

관찰자 객체 그 자체를 리스트 노드로 활용하기 때문에, 관찰자는 한 번에 한 대상만 관찰할 수 있음  
한 대상에 여러 관찰자가 붙는 경우가 훨씬 

 - 리스트 노드 풀  
대상이 관찰자 연결 리스트를 들고 있으나, 노드는 관찰자 객체가 아님  
대신 따로 간단한 노드를 만들어, 관찰자와 다음 노드를 포인터로 가리키게 함  
![image](https://user-images.githubusercontent.com/32252062/70036867-e4a03a00-15f8-11ea-84a1-d2ff59a4191b.png)
(그림 4-4) 대상과 관찰자를 가리키는 노드들의 연결 리스트

같은 관찰자를 여러 노드에서 가리킬 수 있다는 것은, 같은 관찰자를 동시에 여러 대상에 추가할 수 있따는 뜻  
동적 할당을 피하는 방법은, 모든 노드가 같은 자료형에 같은 크기니 객체 풀(19장)에 미리 할당하면 됨  
이러면 고정된 크기의 목록 노드를 확보할 수 있어서 필요할 때 마다 동적 메모리 할당 없이 재사용 가능  

#### 4.5 남은 문제점들

 - 대상과 관찰자 제거  
 관찰자를 부주의하게 삭제하면 대상에 있는 포인터가 이미 삭제된 객체를 가리킬 수 있음  
 대상이 삭제되면 더 이상 알림을 받을 수 없는데도 관찰자는 그것을 모르고 알림을 기다릴 수 있음  
 이런 현상을 막으려면 대상이 삭제되기 직전에 마지막으로 '사망'알림을 보내면 됨  
 
     알림을 받은 관찰자는 필요한 작업을 알아서 하면 됨  
 관찰자는 대상을 삭제하는 것보다 어려움  
 대상이 관찰자를 포인터로 알고 있기 때문  

    가장 쉬운 방법은 관찰자가 삭제될 때 스스로를 등록 취소 하는 것  
 관찰자에서 보통 관찰 중인 대상을 알고 있으므로 소멸자에서 대상의 removeObserver()만 호출하면 됨

 - 관찰자 패턴의 문제  
 프로그램이 제대로 동작하지 않을 때 버그가 여러 관찰자에 퍼져 있따면 상호 흐름을 추론하기가 훨씬 어려워짐  
 코드가 명시적으로 커플링되어 있으면 어떤 메서드가 호출되는지만 보면 되지만, 관찰자 목록을 통해 코드가 커플링 되어있다면 실제로 어떤 관찰자가 알림을 받는지는 **런타임에서** 확인해보는 수밖에 없음  
 이 말은 프로그램에서 코드가 상호작용하는지를 정적으로는 알 수 없고, 명령 실행 과정을 동적으로 추론해야함  

    이러한 문제를 해결하려면 코드를 이해하기 위해 양쪽 코드의 상호작용을 같이 확인해야 할 일이 많다면, 관찰자 패턴 대신 두 코드를 명시적으로 연결하는게 나음  

    **관찰자 패턴은 서로 연관 없는 코드 덩어리들이 하나의 큰 덩어리가 되지 않으면서 서로 상호작용하기에 좋은 방법이지,  
    하나의 기능을 구현하기 위한 코드 덩어리 안에서는 그다지 유용하지 않음**
 
---
---
### CHAPTER 5. 프로토타입
---
#### 스포너 예제  

한 가지 스포너는 한 가지 몬스터 인스턴스만 만든다고 할 때,  
게임에 나오는 모든 몬스터를 지원하기 위해서는 몬스터 클래스마다 스포너 클래스를 만들어야함  
이렇게 하면 스포너 클래스 상속 구조가 몬스터 클래스 상속 구조를 따라가게 됨
![prototype_01](https://user-images.githubusercontent.com/32252062/70121319-aca8fd80-16b1-11ea-9e6d-de02645740eb.png)
(그림 5-1) 동일한 클래스 상속 구조

이런 걸 프로토타입 패턴으로 해결할 수 있음  
핵심은 **어떤 객체가 자기와 비슷한 객체를 스폰할 수 있다는 점**
어떤 몬스터 객체든지 자신과 비슷한 몬스터 객체를 만드는 **원형(prototypal) 객체**로 사용할 수 있음

이를 구현하기 위해, 상위 클래스인 Monster에 **추상 메서드** clone()을 추가
```
class Monster{
public:
    virtual ~Monster() {}
    virtual Monster* clone() = 0;

    // 그 외
};
```

Monster 하위 클래스에서는 자신과 자료형과 상태가 같은 새로운 객체를 반환하도록 clone()을 구현  
예를 들어, 유령 객체라면 다음과 같음
```
class Ghost : public Monster{
public:
    Ghost(int health, int speed)
    : health_(health),
      speed_(speed){
      }
    virtual Monster* clone(){
        return new Ghost(health_, speed_);
    }
private:
    int health_;
    int speed_;
};
```
Monster를 상속받는 모든 클래스에 clone 메서드가 있다면, 스포너 클래스를 종류별로 만들 필요 없이 하나만 만들면 됨

```
class Spawner{
public:
    Spawner(Monster* prototype) : prototype_(prototype) {}
    Monster* spawnMonster(){
        return prototype_->clone();
    }
private:
    Monster* prototype_;
}
```
Spawner 클래스 내부에는 Monster 객체가 숨어 있어 Monster 객체를 도장 찍듯 만들어내는 스포너 역할만 함  

![prototype_02](https://user-images.githubusercontent.com/32252062/70123101-4b832900-16b5-11ea-8700-3c1433e63833.png)
(그림 5-2) 프로토타입을 품고 있는 스포너

유령 스포너를 만들려면 원형으로 사용할 유령 인스턴스를 만든 후에 스포너에 전달
```
Monster* ghostPrototype = new Ghost(15, 3);
Spawner* ghostSpawner = new Spawner(ghostPrototype);
```

프로토타입 패턴의 **좋은 점**은 프로토타입의 클래스뿐만 아니라 **상태도 같이 복제**한다는 점  
즉, 원형으로 사용할 유령 객체를 잘 설정하면 빠른 유령, 약한 유령, 느린 유령용 스포너 같은 것도 쉽게 만들 수 있음  

다만 위의 예제는 현실적이지 않음(요즘은 몬스터마다 클래스를 따로 만들지 않음)  
상속 구조가 복잡하면 유지보수하기 힘듦  
따라서 요즘은 개체 종류별로 클래스를 만들기보다는 컴포넌트(14장)나 타입객체(13장)로 모델링하는 것을 선호

#### 스폰 함수
```
Monster* spawnGhost(){
    return new Ghost();
}
```

```
typedef Monster* (*SpawnCallback)();

class Spawner{
public:
    Spawner(SpawnCallback sapwn) : spawn_(spawn) {}
    Monster* spawnMonster() { return spawn_(); }

private:
    SpawnCallback spawn_;
};
```

유령을 스폰하는 객체는 다음과 같이 만듬  
`Spawner* ghostSpawner = new Spawner(spawnGhost);`

몬스터 클래스를 템플릿 타입 매개변수로 전달하면 특정 몬스터 클래스를 하드코딩 안해도 됨  

```
class Spawner{
public:
    virtual ~Spawner() {}
    virtual Monster* spawnMonster() = 0;
};

template <class T>
class SpawnFor : public Spawner{
public:
    virtual Monster* spawnMonster() { return new T(); }
};
```

---
---
### CHAPTER 6. 싱글턴
---
#### 6.1 싱글턴 패턴  
- 오직 한 개의 인스턴스만 갖도록 보장  
인스턴스가 여러 개면 제대로 작동하지 않는 상황이 종종 있음  
이를 싱글턴으로 만들면 클래스가 인스턴스를 하나만 가지도록 컴파일 단계에서 강제할 수 있음

- 전역 접근점을 제공  
싱글턴은 그 인스턴스를 전역에서 접근할 수 있는 메서드를 제공  
이를 통해서, 누구든지 어디서든지 우리가 만든 인스턴스에 접근 가능  
```
class FileSystem{
public:
    static FileSystem& instance() {
        // 게으른 초기화
        if(instance_ == NULL){
            instance_ = new FileSystem();
        }
        return *instance_;
    }

private:
    FileSystem() {}
    static FileSystem* instance_;
};
```
instance_ 정적 멤버 변수는 클래스 인스턴스를 저장  
생성자가 private이기 때문에 밖에서는 생성할 수 없음  
public에 있는 instance() 정적 메서드는 코드 어디에서나 싱글천 인스턴스에 접근할 수 있게 하고, 싱글턴을 실제로 필요로 할 때까지 인스턴스 초기화를 미루는 역할도 함  
 
```
class FileSystem{
public:
    static FileSystem& instance(){
        static FileSystem *instance = new FileSystem();
        return *intance;
    }

priavte:
    FileSystem() {}
}
```
C++ 11에서는 정적 지역 변수 초기화 코드가 멀티스레드 환경에서도 딱 한 번 실행되어야 하기 때문에 스레드 안전

#### 6.2 싱글턴을 왜 사용하는가?
- 한 번도 사용하지 않는다면 아예 인스턴스를 생성하지 않음  
싱글턴은 처음 사용될 대 초기화만 되므로, 게임 내에서 전혀 사용되지 않으면 아예 초기화되지 않음

- 런타임에 초기화됨  
정적 멤버 변수는 자동 초기화되는 문제가 있음  
즉, 컴파일러는 main 함수를 호출하기 전에 정적 변수를 초기화하기 때문에 프로그램이 실행된 다음에야 알 수 있는(파일로 읽어 들인 설정 값 같은) 정보를 활용할 수 없음  

    게으른 초기화는 이런 문제를 해결  
    싱글턴은 최대한 늦게 초기화되기 때문에, 순환 의존만 없다면, 초기화할 때 다른 싱글턴을 참조해도됨

- 싱글턴을 상속할 수 있음  
파일 시스템 래퍼가 크로스 플랫폼을 지원해야 한다면 추상 인터페이스를 만든 뒤,  
플랫폼마다 구체 클래스를 만들면 됨
```
class FileSystem{
public:
    virtual ~FileSystem() {}
    virtual char* readFile(char* path) = 0;
    virtual void writeFile(char* path, char* contents) = 0;
};
```

플랫폼별로 하위 클래스 정의
```
class PS3Filesystem : public FileSystem{
public:
    virtual char* readFile(char* path){
        // 소니의 파일 IO API를 사용
    }
    virtual void writeFile(char* path, char* contents){
        // 소니의 파일 IO API를 사용
    }
};

class WiiFilesystem : public FileSystem{
public:
    virtual char* readFile(char* path){
        // 닌텐도의 파일 IO API를 사용
    }
    virtual void writeFile(char* path, char* contents){
        // 닌텐도의 파일 IO API를 사용
    }
};
```

FileSystme클래스를 싱글턴으로 만듬
```
class FileSystem{
public:
    static FileSystem& instance();

    virtual ~FileSystem() {}
    virtual char* readFile(char* path) = 0;
    virtual void writeFile(char* path, char* contents) = 0;

protected:
    FileSystem() {}
};
```

핵심은 인스턴스를 생성하는 부분
```
FileSystme& FileSystme::instance() {
#if PLATFORM == PLAYSTATION3
    static FileSystme = *instance = new PS3FileSystem();
#elif PLATFORM == WII
    static FileSystme = *instance = new WiiFileSystem();
#endif
    return *instance;        
}
```

#### 6.3 싱글턴이 왜 문제일까?
짧게 놓고 보면 싱글턴 패턴에는 큰 문제가 없음  
하지만 다른 단기적인 설계 결정들과 마찬가지로 길게 놓고 보면 비용을 지불함  
꼭 필요하지 않은 곳에 싱글턴을 적용하면 다음과 같은 문제에 부딪힘

#### 알고보면 전역 변수
- 전역 변수는 코드를 이해하기 어렵게 함  

- 전역 변수는 커플링을 조장  

- 전역 변수는 멀티스레딩 같은 동시성 프로그래밍에 맞지 않음

#### 싱글턴은 문제가 하나뿐일 때도 두 가지 문제를 풀려 함
싱글턴은 **'한 개의 인스턴스'**와 **'전역 접근'** 두 가지를 한번에 해결하려 함  
하지만 보통은 **'전역 접근'**이 싱글턴 패턴을 선택하는 이유  

Log 클래스를 만들 때, 게임 내 모듈에서 진단 정보를 Log로 남길 수 있다면 편리할 것  
하지만 모든 함수 인수에 Log 클래스 인스턴스를 추가하면 메서드 시그니처가 번잡해지고 코드 의도를 알아보기 어려워짐  

가장 간단한 해결책은 Log 클래스를 싱글턴으로 만들면 모든 함수에서 직접 Log 클래스에 접근해 인스턴스를 얻을 수 있음  
다만 의도치 않게 Log **객체를 하나만 만들 수 있다**는 **제한**이 생김

Log를 파일 하나에 다 쓴다면 인스턴스도 하나만 있으면되나, 모든 분야에서 필요한 정보를 Log로 남기면 파일이 뒤죽박죽 되어버림  
따라서 Log를 여러 파일에 분야별로 나눠 쓸 수 있다면 좋음  
싱글턴을 사용하면 인스턴스를 여러 개 만들 수 없는 문제가 생김  
어디서나 편하게 접근할 수 있따는 장점이 역으로 단점이 되어버림

#### 게으른 초기화는 제어 할 수 없음
가상 메모리를 사용 가능하고, 성능 요구도 심하지 않은 데스크톱 PC에서는 게으른 초기화가 괜찮은 기법  
하지만 게임에서는 다름  
시스템을 초기화 할 때 메모리 할당, 리소스 로딩 등 할 일이 많다 보니 시간이 꽤 걸릴 수 있음  
오디오 시스템 초기화에 일정 시간 이상 걸린다면 초기화 시점을 제어해야함  
처음 소리를 재생할 때 게으른 초기화를 하게 만들면 게임 도중에 초기화가 시작되는 바람에 화면 프레임이 떨어지고 게임이 버벅댈 수 있음

마찬가지로 게임에서는 메모리 단편화를 막기 위해 힙에 메모리를 할당하는 방식을 세밀하게 제어하는 게 보통  
오디오 시스템이 초기화 될 때 상당한 메모리를 힙에 할당하면, 힙 **어디에** 메모리를 할당할지를 제어할 수 있도록 적절한 초기화 **시점**을 찾아야함  

이런 두 가지 문제 때문에 대부분 게임에서는 게으른 초기화를 사용하지 않음  
정적 클래스를 사용하여 해결 가능하면 정적 클래스를 사용  

#### 6.4 대안
#### 클래스가 꼭 필요한가?
싱글턴 클래스 중에는 애매하게 다른 객체 관리용으로만 존재하는 관리자(manager)가 많음  
이는 OOP를 제대로 이해하지 못해 만드는 경우도 많음

서툴게 만든 싱글턴은 다른 클래스에 기능을 더해주는 '도우미'인 경우가 많음  
가능하다면 도우미 클래스에 있던 작동 코드를 모두 원래 클래스로 옮겨야함  
**객체가 스스로를 챙기게 하는게 OOP**  

#### 오직 한 개의 클래스 인스턴스만 갖도록 보장  
이는 싱글턴 패턴이 해결하려는 첫 번째 문제  
인스턴스를 하나만 있도록 보장하는 건 중요, 그렇다고 누구나 어디서든 인스턴스에 접근할 수 있게 하고 싶은건 아닐 수도 있음  

전역 접근 없이 클래스 인스턴스만 한 개로 보장할 수 있는 방법 : 단언문(assert)  
단언문은 코드에 제약을 넣을 때 사용  
assert()는 인수 값을 평가하여 값이 참이면 아무것도 하지 않지만, 거짓이라면 그 자리에서 코드를 중지  

다만 싱글턴은 클래스 문법을 사용해 컴파일 시간에 단일 인스턴스를 보장하지만,  
이 방식에서는 런타임에 인스턴스 개수를 확인한다는 게 단점  

#### 인스턴스에 쉽게 접근하기
쉬운 접근성은 싱글턴을 선택하는 가장 큰 이유  
이런 편리함에는 원치 않는 곳에서도 쉽게 접근할 수 있다는 비용이 따름  

객체에 접근할 수 있는 다른 방법
 - 넘겨주기  
객체를 필요로 하는 함수에 인수로 넘겨주는 게 가장 쉬우면서도 최선인 경우가 많음

    객체를 렌더링하려면 렌더링 상태를 담고 있는 그래픽 디바이스 대표 객체에 접근할 수 있어야함  
이럴 때는 일반적으로 모든 렌더링 함수에서 context 같은 이름의 매개변수를 받음  

    어떤 객체는 메스드 시그니처에 포함되지 않음  
예를 들어 AI 관련 함수에서도 로그를 넘길 수 있어야하지만, AI의 핵심이 아닌 Log 객체를 인수에 추가하기에는 좀 어색

 - 상위 클래스로부터 얻기  
많은 게임에서 클래스를 대부분 한 단계만 상속할 정도로 상속 구조를 얕고 넓게 가져감  
이런 상속 구조에서는 많은 클래스에서 같은 상위 객체에 접근 가능한데, 이를 이용하면 넓은 범위에서 사용 가능한 인스턴스를 만들 수 있음

 - 이미 전역인 객체로부터 얻기  
전역 상태를 모두 제거하기란 너무 이상적  
결국에는 Game이나 World같이 전체 게임 상태를 관리하는 전역 객체와 커플링되어 있기마련  
기존 전역 객체를 사용하면 전역 클래스 개수를 줄일 수 있음  

더 많은 코드가 Game이나 World 클래스에 커플링된다는 단점은 있음

- 서비스 중개자로부터 얻기(16장)  

#### 6.5 싱글턴에 남은 것  
싱글턴을 대체할 패턴  
하위 클래스 샌드박스 패턴(12장) : 클래스가 같은 인스턴스들이 공용 상태를 전역으로 만들지 않고도 접근할 수 있는 방법  
서비스 중개자 패턴(16장) : 객체를 전역으로 접근할 수 있게 하되, 객체를 훨씬 유연하게 설정할 수 있는 방법을 제공

---
---
### CHAPTER 7. 상태  
---
> 객체의 내부 상태에 따라 스스로 행동을 변경할 수 있게 허가하는 패턴, 이렇게 하면 객체는 마치 자신의 클래스를 바꾸는 것처럼 보임

간단한 횡스크롤 플랫포머 게임 만들기
```
void Heroine::handleInput(Input input)
{
  if (input == PRESS_B)
  {
    if (!isJumping_ && !isDucking_)
    {
      // Jump...
    }
  }
  else if (input == PRESS_DOWN)
  {
    if (!isJumping_)
    {
      isDucking_ = true;
      setGraphics(IMAGE_DUCK);
    }
    else
    {
      isJumping_ = false;
      setGraphics(IMAGE_DIVE);
    }
  }
  else if (input == RELEASE_DOWN)
  {
    if (isDucking_)
    {
      // Stand...
    }
  }
}
```

무언가를 검사하려면 계속해서 플래스 변수를 만들어야 함  
이렇게되면 조금만 건드리면 망가짐  

![state_01](https://user-images.githubusercontent.com/32252062/70800882-ba1c6100-1df0-11ea-93a1-76ff390969d7.png)
(그림 7-1) 상태 기계를 그린 플로차트

위는 **유한 상태 기계(FSM)**임, FSM은 컴퓨터 과학 분야 중의 하나인 **오토마타 이론**에서 나옴  
 - 가질수 있는 **'상태'**가 한정
 - 한 번에 **'한 가지'** 상태만 될 수 있음
 - '입력'이나 '이벤트'가 **기계에 전달**됨
 - 각 상태에는 **입력에 따라 다음 상태로 바뀌는 '전이'**가 있음

#### 열거형과 다중 선택문  
여러 플래그 변수 중에서 하나만 참일 때가 많다면 **열거형(enum)**이 필요  

이전에는 입력에 따라 먼ㅁ저 분기한 뒤에 상태에 따라 분기  
따라서 하나의 버튼 입력에 대한 코드는 모아둘 수 있었으나, 하나의 상태에 대한 코드는 흩어져 있었음  
상태 관련 코드를 한곳에 모아두기 위해 먼저 상태에 따라 분기  
```
void Heroine::handleInput(Input input)
{
  switch (state_)
  {
    case STATE_STANDING:
      if (input == PRESS_B)
      {
        state_ = STATE_JUMPING;
        yVelocity_ = JUMP_VELOCITY;
        setGraphics(IMAGE_JUMP);
      }
      else if (input == PRESS_DOWN)
      {
        state_ = STATE_DUCKING;
        setGraphics(IMAGE_DUCK);
      }
      break;

    case STATE_JUMPING:
      if (input == PRESS_DOWN)
      {
        state_ = STATE_DIVING;
        setGraphics(IMAGE_DIVE);
      }
      break;

    case STATE_DUCKING:
      if (input == RELEASE_DOWN)
      {
        state_ = STATE_STANDING;
        setGraphics(IMAGE_STAND);
      }
      break;
  }
}
```

열거형은 상태 기계를 구현하는 가장 간단한 방법이고, 이 정도만으로 충분할 때도 꽤 있음  

열거형만으로 부족할 수도 있음  
엎드려서 기를 모아 특수공격을 한다고 가정하면, 엎드려서 기를 모으는 시간을 기록해야함  
이를 위해 Heroin에 chargeTime_필드를 추가  

```
void Heroine::update()
{
  if (state_ == STATE_DUCKING)
  {
    chargeTime_++;
    if (chargeTime_ > MAX_CHARGE)
    {
      superBomb();
    }
  }
}
```
엎드릴 때마다 시간 초기화
```
void Heroine::handleInput(Input input)
{
  switch (state_)
  {
    case STATE_STANDING:
      if (input == PRESS_DOWN)
      {
        state_ = STATE_DUCKING;
        chargeTime_ = 0;
        setGraphics(IMAGE_DUCK);
      }
      // Handle other inputs...
      break;

      // Other states...
  }
}
```
기 모으기 공격을 추가하기 위해 함수 두 개를 수정하고 엎드리기 상태에서만 의미 있는 chargeTime_필드를 Heroine에 추가했어야함  
이보다는 모든 코드와 데이터를 한 곳에 모아둘 수 있는게 나음  

#### 상태패턴
모든 분기문을 동적 디스패치(가상 함수)로 바꾸려는 사람이 있지만, 때로는 **if문**만으로도 충분함

#### 상태 인터페이스
상태에 의존하는 모든 코드, 즉, 다중 선택문에 있던 동작을 인터페이스의 가상 메서드로 만듬  
```
class HeroineState
{
public:
  virtual ~HeroineState() {}
  virtual void handleInput(Heroine& heroine, Input input) {}
  virtual void update(Heroine& heroine) {}
};
```

#### 상태별 클래스 만들기
상태별로 인터페이스를 구현하는 클래스도 정의  
메서드에는 정해진 상태가 되었을 때 주인공이 어떤 행동을 할지를 정의  
다중 선택문에 있떤 case별로 클래스를 만들어 코드를 옮기면 됨
```
class DuckingState : public HeroineState
{
public:
  DuckingState()
  : chargeTime_(0)
  {}

  virtual void handleInput(Heroine& heroine, Input input) {
    if (input == RELEASE_DOWN)
    {
      // Change to standing state...
      heroine.setGraphics(IMAGE_STAND);
    }
  }

  virtual void update(Heroine& heroine) {
    chargeTime_++;
    if (chargeTime_ > MAX_CHARGE)
    {
      heroine.superBomb();
    }
  }

private:
  int chargeTime_;
};
```
chargeTime_ 변수를 Heroin에서 DuckingState 클래스로 옮겨서 엎드리기 상태에서만 의미 있따는 점을 객체 모델링을 통해서 분명하게 보여줌  

#### 동작을 상태에 위임  
Heroin 클레스에 자신의 현재 상태 객체 포인터를 추가해, 거대한 다중 선택문은 제거하고 대신 상태 객체에 위임  

```
class Heroine
{
public:
  virtual void handleInput(Input input)
  {
    state_->handleInput(*this, input);
  }

  virtual void update()
  {
    state_->update(*this);
  }

  // Other methods...

private:
  HeroineState* state_;
};
```
상태를 바꾸려면 state_ 포인터에 HeroineState를 상속받는 다른 객체를 할당하기만하면 됨

#### 7.5 상태 객체는 어디에 둬야 할까?
상태 패턴은 클래스를 쓰기 때문에 포인터에 저장할 실제 인스턴스가 필요  
이를 위해 두 가지 방법이 있음  

#### 정적 객체
상태 객체에 필드가 따로 없다면 가상 메서드 호출에 필요한 vtable 포인터만있는 셈  
이럴 경우 모든 인스턴스가 같기 때문에 인스턴스는 하나만 있으면 됨  
정적 인스턴스는 원하는 곳에 두면 됨  
```
class HeroineState
{
public:
  static StandingState standing;
  static DuckingState ducking;
  static JumpingState jumping;
  static DivingState diving;

  // Other code...
};
```
각각의 정적 변수가 게임에서 사용하는 상태 인스턴스임  
서 있는 상태에서 점프하게 하려면 다음과 같이 하면 됨  
```
if (input == PRESS_B)
{
  heroine.state_ = &HeroineState::jumping;
  heroine.setGraphics(IMAGE_JUMP);
}
```

#### 상태 객체 만들기
정적 객체만으로는 캐릭터가 하나라면 가능하지만, 한 화면에 두 개 이상의 캐릭터가 보일 경우에는 문제가 됨  
이럴 때는 전이할 때마다 상태 객체를 만들어서 FSM이 상태별로 인스턴스를 갖게 만들 수 있음  

새로 상태를 할당했기 때문에 이전 상태를 해제해야하는데, 삭제할 때 this를 스스로 지우지 않도록 주의  

```
void Heroine::handleInput(Input input)
{
  HeroineState* state = state_->handleInput(*this, input);
  if (state != NULL)
  {
    delete state_;
    state_ = state;
  }
}
```

```
HeroineState* StandingState::handleInput(Heroine& heroine, Input input)
{
  if (input == PRESS_DOWN)
  {
    // Other code...
    return new DuckingState();
  }

  // Stay in this state.
  return NULL;
}
```

#### 7.6 입장과 퇴장
상태 패턴의 목표는 같은 상태에 대한 모든 동작과 데이터를 클래스 하나에 캡슐화하는 것  
```
HeroineState* DuckingState::handleInput(Heroine& heroine, Input input)
{
  if (input == RELEASE_DOWN)
  {
    heroine.setGraphics(IMAGE_STAND);
    return new StandingState();
  }

  // Other code...
}
```
지금까지는 이전 상태에서 스프라이트를 변경했지만, 상태에서 그래픽까지 제어하는게 바람직함  

```
class StandingState : public HeroineState
{
public:
  virtual void enter(Heroine& heroine)
  {
    heroine.setGraphics(IMAGE_STAND);
  }

  // Other code...
};
```
Heroin 클레스에서 새로운 상태에 들어있는 enter 함수를 호출하도록 상태 변경 코드를 수정

```
void Heroine::handleInput(Input input)
{
  HeroineState* state = state_->handleInput(*this, input);
  if (state != NULL)
  {
    delete state_;
    state_ = state;

    // 새로운 상태의 입장 함수를 호출
    state_->enter(*this);
  }
}
```
엎드리기 코드를 더욱 단순하게 만들 수 있음

```
HeroineState* DuckingState::handleInput(Heroine& heroine, Input input)
{
  if (input == RELEASE_DOWN)
  {
    return new StandingState();
  }

  // Other code...
}
```
상태가 새로운 상태로 교체되기 직전에 호출되는 퇴장 코드도 이런 식으로 활용 가능

#### 7.7 FSM의 단점
인공지능같이 더 복잡한 곳에 적용하다 보면 한계에 부딪힘  
이를 해결하기 위해 다음과 같은 방법이 있음

#### 7.8 병행 상태 기계
만약에 주인공이 총을 들 수 있다면, 앞에서 만든 FSM 방식으로는 모든 상태를 무장, 비무장에 맞춰 두 개씩 만들어야함  

두 종류의 상태, 즉 무엇을 하는가와 무엇을 들고 있는가를 한 상태 기계에 욱여넣다 보니 생기는 문제임  

상태 기계를 둘로 나누면 문제를 해결할 수 있음  
무엇을 하는가에 대한 상태 기계는 그대로 두고, 무엇을 들고 있는가에 대한 상태 기계를 따로 정의  
Heroin 클래스는 이들 '상태'를 **각각** 참조  
(사실 무기 장착에는 bool만 사용하여 표현해도 됨)
```
class Heroine
{
  // Other code...

private:
  HeroineState* state_;
  HeroineState* equipment_;
};
```
Heroin에서 입력을 상태에 위암할 때에는 입력을 **상태 기계 양쪽에 다 전달**  
```
void Heroine::handleInput(Input input)
{
  state_->handleInput(*this, input);
  equipment_->handleInput(*this, input);
}
```

두 상태 기계가 서로 전혀 연관이 없다면 이 방법이 좋음

#### 7.9 계층형 상태 기계
동작에 살을 덧붙이다보면 서기, 걷기, 달리기, 미끄러지기 같은 비슷한 상태가 많이 생김  
단순한 상태 기계 구현에서는 이런 코드를 모든 상태마다 중복해 넣어야함  
그보다는 한 번만 구현하고 다른 상태에서 재사용하는게 나음  

점프와 엎드리기는 '땅 위에 있는' 상태 클래스를 정의해서 처리  
서기, 걷기, 달리기, 미끄러지기는 '땅 위에 있는' 상태 클래스를 상속받아 고유 동작을 추가하면 됨  

이러한 구조를 계층형 상태 기계라고 함  

어떤 상태는 상쉬 상태를 가질 수 잇고, 그 경우 그 상태 자신은 하위 상태가 됨  
이벤트가 들어올 때 하위 상태에서 처리하지 않으면 상위 상태로 넘어감

상위 상태용 클래스를 하나 정의
```
class OnGroundState : public HeroineState
{
public:
  virtual void handleInput(Heroine& heroine, Input input)
  {
    if (input == PRESS_B)
    {
      // Jump...
    }
    else if (input == PRESS_DOWN)
    {
      // Duck...
    }
  }
};
```
그 다음 각각의 하위 상태가 상위 상태를 상속 받음
```
class DuckingState : public OnGroundState
{
public:
  virtual void handleInput(Heroine& heroine, Input input)
  {
    if (input == RELEASE_DOWN)
    {
      // Stand up...
    }
    else
    {
      // Didn't handle input, so walk up hierarchy.
      OnGroundState::handleInput(heroine, input);
    }
  }
};
```
클래스를 사용하는 GoF식 상태 패턴을 쓰지 않는다면 이런 구현이 불가능  
그럴 땐 클래스에 상태를 하나만 두지 않고 상태 스택을 만들어 명시적으로 현재 상태의 상위 상태 연쇄를 모델링 할 수도 있음  
현재 상태가 스택 최상위에 있고, 밑에는 바로 위 상위 상태가 있으며, 그 상위 상태 밑에는 그 상위 상태의 상위 상태가 있는 식  
상태 관련 동작이 들어오면 어느 상태든 동작을 처리할 때까지 스택 위에서부터 밑으로 전달

#### 7.10 푸시다운 오토마타
상태 스택을 활용하여 FSM을 활장하는 다른 방법  

FSM에는 이력 개념이 없다는 문제가 있음  
현재 상태는 알 수 있지만, 직전 상태가 무엇인지를 따로 저장하지 않기 때문에 이전 상태로 쉽게 돌아갈 수 없음

만약에 주인공이 총을 쏜다면 어려운 부분은 총을 쏜 뒤에 어느 상태로 돌아가야 하는가 하는 점  
일반적인 FSM에서는 이전 상태를 알 수 없어서 총 쏘기가 끝낫을 때 되돌아갈 상태를 하드코딩 해야함  

이것보다는 총 쏘기 전 상태를 저장해놨다가 나중에 불러와 써먹는게 훨씬 나음  

이럴 때 써먹을 만한 것이 **푸시다운 오토마타**  

FSM이 **한 개**의 상태를 포인터로 관리했다면 푸시다운 오토마타에서는 상태를 **스택**으로 관리함  
FSM은 이전 상태를 덮어쓰고 새로운 상태로 전이하는 방식  
푸시다운 오토마타에서는 이외에도 부가적인 명령이 두 가지 더 있음  
 - 새로운 상태를 스택에 넣음(push)  
    스택의 최상위 상태가 '현재'상태이기 때문에, 새로 추가된 상태가 현재 상태가 됨
    단, 이전 상태는 버리지 않고 방금 들어온 최신 상태 밑에 있게 됨
 - 최상위 상태를 스택에서 뺌(pop)  
    빠진 상태는 제거되고, 바로 밑에 있던 상태가 새롭게 '현재'상태가 됨  

![image](https://user-images.githubusercontent.com/32252062/71174852-7ec6da00-22a9-11ea-9262-a03c0bf3a66e.png)
(그림 7-2) 넣기와 빼기

총 쏘기 상태를 구현할 때, 발사 버튼을 누르면 총 쏘기 상태를 스택에 넣고, 총 쏘기 애니메이션이 끝날 때 총 쏘기 상태를 스택에서 빼면, 푸시다운 오토마타가 알아서 이전 상태로 보내 줌  

---
---
## Part 3. 순서 패턴  
---
### CHAPTER 8. 이중 버퍼  
---
#### 8.1, 8.2 의도 및 동기  
여러 순차 작업의 결과를 한 번에 보여줌  

컴퓨터는 큰 일을 작은 단계로 쪼개어 하나씩 처리할 수 있음(**순차적**으로 동작)  
사용자 입장에서는 순차적으로 혹은 동시에 진행되는 여러 작업을 한 번에 모아서 봐야 할 때가 있음  
예를 들어, 게임에서 유저가 화면을 볼 때, 화면을 그리는 과정이 보이면 몰입할 수 없음  
**이중 버퍼**는 이런 문제를 해결 가능  

#### 간단한 컴퓨터 그래픽스 작동 원리
대부분의 컴퓨터에서는 픽셀을 **프레임버퍼**로부터 가져오기 때문에 어떤 색을 어디에 뿌려야 할지 알 수 있음  
**프레임버퍼**는 메모리에 할당된 픽셀들의 배열로, 한 픽셀의 색을 여러 바이트로 표현하는 RAM의 한 부분  
화면에 색을 뿌릴 때 프레임버퍼로부터 한 바이트씩 색깔 값을 읽어옴  

궁극적으로 게임을 화면에 보여주려면 프레임버퍼에 값을 써 넣으면 되나, 사소한 문제가 하나 있음  
렌더링 코드가 실행되는 동안 보통은 다른 작업을 실행되지 않으나, 렌더링 도중에 실행되는 작업이 일부 있음  
그중 하나가 게임이 실행되는 동안 디스플레이가 프레임버퍼를 반복해서 읽는 것인데, 여기서 문제가 발생함   

화면에 웃는 얼굴을 하나 그린다고 할 때, 코드에서는 루프를 돌면서 프레임버퍼에 필셀 값을 입력함  
코드가 프레임버퍼에 값을 쓰는 도중에도 비디오 드라이버에서 프레임버퍼 값을 읽음  
우리가 입력해놓은 픽셀 값을 비디오 드라이버가 화면에 출력하면서 웃는 얼굴이 나오기 시자하지만,  
아직 다 입력하지 못한 퍼버 값까지 화면에 출력할 수 있음  

그 결과 그림 일부만 나타나는 테어링이 발생함  

![image](https://user-images.githubusercontent.com/32252062/72338721-6c08bf00-3708-11ea-9f48-6b32cbfd5896.png)
(그림 8-1) 렌더링 도중 나타나는 테어링

이 문제를 해결하기 위해서 코드에서는 픽셀을 한 번에 하나씩 그리되, 비디오 드라이버는 전체 픽셀을 한 번에 다 읽을 수 있게 해줘야함  
즉, 이전 프레임에는 얼굴 그림이 하나도 안보이다가, 다음 프레임에 전체가 보여야함  
**이중 버퍼 패턴**이 이런 문제를 해결할 수 있음  

이중 버퍼는 프레임버퍼를 **두 개** 준비해, 하나의 버퍼에는 지금 프레임에 보일 값을 둬서 GPU가 원할 때 언제든지 읽을 수 있게함  
그동안 렌더링 코드는 **다른** 프레임버퍼를 채움  
렌더링 코드가 장면을 다 그린 후에는 조명을 바꾸는 것처럼 버퍼를 교체한 뒤에 비디어 하드웨어에 두 번째 버퍼를 읽으라고 알려줌  
화면 깜빡임에 맞춰 버퍼가 바뀌기 때문에 테어링은 더 이상 생기지 않고 전체 장면이 한 번에 나타남  

#### 8.3 패턴
**버퍼 클래스**는 변경이 가능한 상태인 **버퍼**를 캡슐화함  
버퍼는 점차적으로 수정되나, 밖에서는 한 번에 바뀌는 것처럼 보이게 하고 싶음  
이를 위해서 버퍼 클래스는 **현재 버퍼**와 **다음 버퍼**, 두 개의 버퍼를 가짐

 - 정보를 읽을 때는 항상 **현재** 버퍼에 접근  
 - 정보를 쓸때는 항상 **다음** 버퍼에 접근  
 - 변경이 끝나면 다음 버퍼와 현재 버퍼를 교체(현재 버퍼는 새로운 다음 버퍼가 되어 재사용)  

#### 8.4 언제 쓸것인가?
이중 버퍼 패턴은 구체적으로 다음 같은 상황에서 적합함  
 - 순차적으로 변경해야 하는 상태가 있을 때
 - 이 상태는 변경 도중에도 접근 가능해야 함
 - 바깥 코드에서는 작업 중인 상태에서 접근할 수 없어야함
 - 상태에 값을 쓰는 도중에도 기다리지 않고 바로 접근할 수 있어야함

#### 8.5 주의사항
다른 대규모 아키텍처용 패턴과는 달리 이중 버퍼는 코드 구현 수준에서 적용되기 때문에 코드 전체에 미치는 영향이 적은 편이고 다들 비슷비슷하게 쓰고 있음  
그래도 몇 가지 주의할 점은 있음

#### 교체 연산 자체에 시간이 걸림
이중 버퍼 패턴에서는 버퍼에 값을 다 입력했다면 버퍼를 교체해야 함  
교체 연산은 원자적이어야함, 즉 교체 중에는 두 버퍼 **모두에** 접근할 수 없어야 함  
대부분은 포인터만 바꾸면 되기 때문에 충분히 빠르나, 혹시라도 버퍼에 값을 쓰는 것보다 교체가 더 오래걸린다면 이중 버퍼 패턴이 아무런 도움이 안됨  

#### 버퍼가 두 개 필요함
이중 버퍼 패턴은 메모리가 더 필요함  
상태를 메모리 버퍼 두 곳에 항상 쌍으로 가지고 있어야 하기 때문에 메모리가 부족한 기기에서는 굉장히 부담이 될 수 있음  
메모리가 부족해 버퍼를 두 개 만들기 어렵다면 이중 버퍼 패턴을 포기하고 상태를 변경하는 동안 밖에서 접근하지 못하게 할 방법을 찾아야함  

#### 8.6 예제 코드
```
class Framebuffer
{
public:
  Framebuffer() { clear(); }

  void clear()
  {
    for (int i = 0; i < WIDTH * HEIGHT; i++)
    {
      pixels_[i] = WHITE;
    }
  }

  void draw(int x, int y)
  {
    pixels_[(WIDTH * y) + x] = BLACK;
  }

  const char* getPixels()
  {
    return pixels_;
  }

private:
  static const int WIDTH = 160;
  static const int HEIGHT = 120;

  char pixels_[WIDTH * HEIGHT];
};
```
Framebuffer 클래스는 clear() 메서드로 전체 버퍼를 흰색으로 채우거나, draw()메서드로 특정 픽셀에 검은색을 입력할 수 있음  
getPixels() 메서드를 통해 픽셀 데이터를 담고 있는 메모리 배열에 접근할 수 있음  
비디오 드라이버가 화면을 그리기 위해 버퍼 값을 읽을 때 호출하는 것이 getPixels()  

이걸 Scene 클래스 안에 넣음  
Scene 클래스에서는 여러 번 draw()를 호출해 버퍼에 원하는 그림을 그림
```
class Scene
{
public:
  void draw()
  {
    buffer_.clear();

    buffer_.draw(1, 1);
    buffer_.draw(4, 1);
    buffer_.draw(1, 3);
    buffer_.draw(2, 4);
    buffer_.draw(3, 4);
    buffer_.draw(4, 3);
  }

  Framebuffer& getBuffer() { return buffer_; }

private:
  Framebuffer buffer_;
};
```

![image](https://user-images.githubusercontent.com/32252062/72340085-e4707f80-370a-11ea-92fb-1e0bc31d4607.png)  
(그림 8-2) 얼굴  

게임 코드는 매 프레임마다 어떤 장면을 그려야 할지를 알려줌  
먼저 버퍼를 지운 뒤에 한 번에 하나씩 그리고자 하는 픽셀을 찍음  
동시에 비디오 드라이버에서 내부 버퍼에 접근할 수 있도록 getBuffer()를 제공  

하지만 이것만으로는 비디오 드라이버가 아무때나 getPixel()를 호출해 버퍼에 접근하는 문제가 생길 수 있음  
```
buffer_.draw(1, 1);
buffer_.draw(4, 1);
// <- 이때 비디오 드라이버가 픽셀 전체를 읽을 수도 있음
buffer_.draw(1, 3);
buffer_.draw(2, 4);
buffer_.draw(3, 4);
buffer_.draw(4, 3);
```
이런 일이 벌어지면 테어링이 생김  

```
class Scene
{
public:
  Scene()
  : current_(&buffers_[0]),
    next_(&buffers_[1])
  {}

  void draw()
  {
    next_->clear();

    next_->draw(1, 1);
    // ...
    next_->draw(4, 3);

    swap();
  }

  Framebuffer& getBuffer() { return *current_; }

private:
  void swap()
  {
    // Just switch the pointers.
    Framebuffer* temp = current_;
    current_ = next_;
    next_ = temp;
  }

  Framebuffer  buffers_[2];
  Framebuffer* current_;
  Framebuffer* next_;
};
```
버퍼에 접근할 때는 배열 대신 next_와 current_ 포인터 멤버 변수로 접근  
렌더링 할 때는 next_ 포인터가 가리키는 다음 버퍼에 그리고, 비디오 드라이버는 current_ 포인터로 현재 버퍼에 접근해 픽셀을 가져옴  

이런식으로 비디오 드라이버가 작업 중인 버퍼에 접근하는 걸 막을 수 있음  

장면을 다 그린 후에 swap()을 호출하면 됨  

#### 그래픽스 외의 활용법
변경 중인 상태에 접근할수 있다는 게 이중 버퍼로 해결하려는 문제의 핵심  
원인은 보통 두 가지
1. 다른 스레드나 인터럽트에서 상태에 접근하는 경우  
  → 그래픽스 예제에서 살펴봄
2. **어떤 상태를 변경하는 코드**가 동시에 지금 변경하려는 상태를 읽는 경우  
  → 물리나 인공지능같이 객체가 서로 상호작용할 때 이런 경우를 쉽게 볼 수 있음  

#### 멍청한 인공지능  
```
class Actor
{
public:
  Actor() : slapped_(false) {}

  virtual ~Actor() {}
  virtual void update() = 0;

  void reset()      { slapped_ = false; }
  void slap()       { slapped_ = true; }
  bool wasSlapped() { return slapped_; }

private:
  bool slapped_;
};
```
매 프레임마다 배우 객체의 update()를 호출해 배우가 뭔가를 진행할 수 있게 해줘야함  
특히 유저 입장에서는 **모든 배우가 한 번에 업데이트되는 것처럼 보여야 함**  

배우는 서로 상호작용 할 수 있음  
update()가 호출될 때 배우는 다른 배우 객체의 slap()을 호출해 때리고, wasSlapped()를 통해서 맞았는지 여부를 알 수 있음  

```
class Stage
{
public:
  void add(Actor* actor, int index)
  {
    actors_[index] = actor;
  }

  void update()
  {
    for (int i = 0; i < NUM_ACTORS; i++)
    {
      actors_[i]->update();
      actors_[i]->reset();
    }
  }

private:
  static const int NUM_ACTORS = 3;

  Actor* actors_[NUM_ACTORS];
};
```
Stage 클래스는 배우를 추가할 수 있고, 관리하는 배우 전체를 업데이트할 수 있는 update() 메서드를 제공  
유저 입장에서는 배우들이 한 번에 움직이는 것처럼 보이겠지만, 내부적으로는 하나씩 업데이트

```
class Comedian : public Actor
{
public:
  void face(Actor* actor) { facing_ = actor; }

  virtual void update()
  {
    if (wasSlapped()) facing_->slap();
  }

private:
  Actor* facing_;
};
```
Actor를 상속받는 구체 클래스 Comedian을 정의  
Comedian은 다른 배우 한 명을 바라보고 있다가 누구한테든 맞으면 보고 있던 배우를 때림  

```
Stage stage;

Comedian* harry = new Comedian();
Comedian* baldy = new Comedian();
Comedian* chump = new Comedian();

harry->face(baldy);
baldy->face(chump);
chump->face(harry);

stage.add(harry, 0);
stage.add(baldy, 1);
stage.add(chump, 2);
```
무대는 다음 그림과 같음, 화살표는 누가 누구를 보고 있는지 보여주고 숫자는 배열에서의 인덱스임  

![image](https://user-images.githubusercontent.com/32252062/72341367-6366b780-370d-11ea-8e01-bc7091369931.png)  
(그림 8-3) 게임에서 벌어지는 일  

```
harry->slap();

stage.update();
```
Stage 클래스의 update 메서드는 순서대로 돌아가면서 배우 객체의 update()를 호출하기 때문에, 코드가 실행된 후에는 이런 결과를 얻게 됨

- Stage가 actor 0인 Harry를 업데이트  
  → Harry가 맞음, 그래서 Baldy를 때림
- Stage가 actor 1인 Baldy를 업데이트  
  → Baldy가 맞음, 그래서 Chump를 때림
- Stage가 actor 2인 Chump를 업데이트  
  → Chump가 맞음, 그래서 Harry를 때림
- Stage 업데이트 끝

처음에 Harry를 때린 것이 한 프레임만에 전체 코미디언에게 전파됨  
이번에는 바라보는 대상은 유지하되 Stage 배열 내에서의 위치를 바꿔봄  

![image](https://user-images.githubusercontent.com/32252062/72341551-e556e080-370d-11ea-8f18-460df337d8a3.png)  
(그림 8-4) 위치를 변경한 상황  

무대를 초기화하는 코드에서 나머지는 그대로 두고 무대에 배우를 추가하는 코드만 다음과 같이 바꿈  
```
stage.add(harry, 2);
stage.add(baldy, 1);
stage.add(chump, 0);
```

다시 Harry를 때린 후 어떤 일이 벌어지는지 보면
- Stage가 actor 0인 Chump를 업데이트  
  → Chump가 맞지 않음, Chump는 아무것도 하지 않음
- Stage가 actor 1인 Baldy를 업데이트  
  → Baldy가 맞지 않음, Baldy는 아무것도 하지 않음
- Stage가 actor 2인 Harry를 업데이트  
  → Harry가 맞음, 그래서 Baldy를 때림
- Stage 업데이트 끝

배우 전체를 업데이트할 때 배우의 맞은 상태(slapped_)를 바꾸는데, 그와 동시에 같은 값을 읽기도 하다 보니 업데이트 초반에 맞은 상태를 바꾼게 나중에 가서 영향을 미치게 됨  

결과적으로 배우들이 맞았을 때 배치 순서에 따라 반응하는 프레임이 달라질 수 있음  
배우들이 동시에 행동하는 것처럼 보이고 싶었는데, 업데이트 순서에 따라 결과가 달라지면 안됨  

#### 맞은 상태를 버퍼에 저장  
여기에서도 이중 버퍼 패턴을 써먹을 수 있음  
통짜 '버퍼' 객체 두 개 대신, 더 정교하게 배우의 '맞은' 상태만 버퍼에 저장  
```
class Actor
{
public:
  Actor() : currentSlapped_(false) {}

  virtual ~Actor() {}
  virtual void update() = 0;

  void swap()
  {
    // Swap the buffer.
    currentSlapped_ = nextSlapped_;

    // Clear the new "next" buffer.
    nextSlapped_ = false;
  }

  void slap()       { nextSlapped_ = true; }
  bool wasSlapped() { return currentSlapped_; }

private:
  bool currentSlapped_;
  bool nextSlapped_;
};
```
그래픽스 예제처럼 현재 상태(currentSlapped_)는 **읽기 용도**로, 다음 상태(nextSlapped_)는 **쓰기 용도**로 사용  
rest() 메서드 대신에 swap() 메서드가 생김, swap()은 다음 상태를 현재 상태로 복사한 후 다음 상태를 초기화  
```
void Stage::update()
{
  for (int i = 0; i < NUM_ACTORS; i++)
  {
    actors_[i]->update();
  }

  for (int i = 0; i < NUM_ACTORS; i++)
  {
    actors_[i]->swap();
  }
}
```
이제 Stage의 update() 메서드는 모든 배우를 먼저 업데이트한 **다음에** 상태를 교체  
결과적으로 배우 객체는 자신이 맞았다는 것을 **다음** 프레임에서야 알 수 있음  

#### 8.7 디자인 결정
이중 버퍼 패턴을 구현할 때 결정해야 할 중요한 점 두 가지가 있음

#### 버퍼를 어떻게 교체할 것인가?
 - 버퍼 포인터나 레퍼런스를 교체
    - 빠름  
      버퍼가 아무라 커도 포인터 두 개만 바꿔주면 됨

    - 버퍼 코드 밖에서는 버퍼 메모리를 포인터로 저장할 수 없다는 한계가 있음  
      이 방식에서는 데이터를 실제로 옮기지 않고, 주기적으로 다른 버퍼를 읽으라고 알려줌  
      버퍼 외부 코드에서 버퍼 내 데이터를 직접 포인터로 저장하면 버퍼 교체 후 잘못된 데이터를 가리킬 가능성이 있음  
      특히 비디오 드라이버가 프레임 버퍼는 항상 메모리에서 같은 위치에 있을 거라고 기대하는 시스템에서 문제가 됨  
      이런 시스템에서는 이중 버퍼를 쓸 수 없음

    - 버퍼에 남아 있는 데이터는 바로 이전 프레임 데이터가 아닌 2프레임 전 데이터임  
      ```
      버퍼 A에 프레임 1을 그림  
      버퍼 B에 프레임 2을 그림  
      버퍼 A에 프레임 3을 그림  
      ```
      프레임 3을 그릴 때 버퍼 A에 남아있는 데이터는 프레임 2가 아닌 프레임 1에서 그린 것임을 알 수 있음  
      일반적으로는 그리기 전에 버퍼를 정리하기 때문에 별 문제가 안되나,  
      버퍼에 남은 데이터를 재사용할 때는 이 데이터가 2프레임 전 데이터라는 걸 감안해야 함
 - 버퍼끼리 데이터를 복사
    - 다음 버퍼에는 딱 한 프레임 전 데이터가 들어 있음  
      이전 버퍼에서 좀 더 최신 데이터를 얻을 수 있다는 장점

    - 교체 시간이 더 걸림  
      가장 큰 단점  
      교체를 하려면 전체 버퍼를 다 복사해야함  
      프레임 버퍼의 크기가 크다면 엄청난 시간이 걸릴 수 있음  
      그동안 양쪽 버퍼에 읽고 쓰기가 불가능하기 때문에 제약이 큼

#### 얼마나 정밀하게 버퍼링할 것인가?
버퍼가 **하나의 데이터**인가, 아니면 **객체 컬렉션 안에 분산**되어 있는가에 따라 다름  
(전자는 그래픽 예제, 후자는 배우 예제)  
 - 버퍼가 한 덩어리라면
    - 간단히 교체할 수 있음  
      버퍼 두 개만 있기 때문에 한 번에 맞바꾸기만 하면 됨

 - 여러 객체가 각종 데이터를 들고 있다면  
    - 교체가 더 느림  
      전체 객체 컬렉션을 순회하면서 교체하라고 알려줘야 함  
      만약 이전 버퍼를 건드리지 않아도 된다면, 버퍼가 여러 객체에 퍼져 있어도 단일 버퍼와 같은 성능을 낼 수 있도록 간단하게 최적화할 방법이 있음  
      현재와 다음 포인터 개념을 객체 상대적 **오프셋**으로 응용
      ```
      class Actor
      {
      public:
        static void init() { current_ = 0; }
        static void swap() { current_ = next(); }

        void slap()        { slapped_[next()] = true; }
        bool wasSlapped()  { return slapped_[current_]; }

      private:
        static int current_;
        static int next()  { return 1 - current_; }

        bool slapped_[2];
      };
      ```
      배우는 상태 배열의 current_ 인덱스를 통해 맞은 상태에 접근  
      다음 상태는 배열의 나머지 한 값이므로 next()로 인덱스를 계산  
      상태 교체는 current_ 값을 바꾸기만 하면 됨  
      여기서 swap()이 '정적' 함수이기 때문에 한 번만 호출해도 '모든'배우의 상태를 교체할 수 있다는 게 핵심  

---
---
### CHAPTER 9. 게임 루프  
---
#### 9.1, 9.2 의도 및 동기
*게임 시간 진행*을 *유저 입력*, *프로세서 속도*와 **디커플링**  

#### 이벤트 루프
최신 GUI 애플리케이션도 내부를 들여다보면 옛날과 비슷함  
문자 입력 대신 마우스나 키보드 입력 이벤트를 기다린다는 점 외에는 사용자 입력을 받을 때까지 멈춰서 대기하는 동작 방식에서 별 차이가 없음  
```
while (true)
{
  Event* event = waitForEvent();
  dispatchEvent(event);
}
```

하지만 게임은 유저 입력 없이도 계속 돌아감  
**루프에서 사용자 입력을 처리하지만 마냥 기다리고 있지 않는다는 점**이 게임 루프의 첫 번째 핵심  
```
while (true)
{
  processInput();
  update();
  render();
}
```
기본 코드는 크게 달라지지 않음  
processInput()은 이전 호출 이후 들어온 유저 입력을 처리  
update()는 게임 시뮬레이션을 한 단계 시뮬레이션(보통 AI와 물리 순서로 처리)  
render()는 플레이어가 어떤 일이 벌어지는지 알 수 있도록 게임 화면을 그림

#### 게임 월드에서의 시간
게임 루프가 돌 때마다 게임 상태는 조금씩 진행됨, 이는 게임 시간이 흘러가는 셈  
실제 게임 동안 게임 루프가 얼마나 많이 돌았는지를 측정하면 '초당 프레임 수'(FPS)를 얻을 수 있음  
게임 루프가 빠르게 돌면 FPS가 올라가면서 부드럽고 빠른 화면을  
게임 루프가 느리면 뚝뚝 끊겨보임  

앞의 코드는 무조건 빠르게 루프를 돌기 때문에 두 가지 요인이 프레임 레이트를 결정함  
1. 한 프레임에 얼마나 많은 작업을 하는가  
  CPU, GPU가 계속 바쁘다면 한 프레임에 걸리는 시간이 늘어남
2. 코드가 실행되는 플랫폼의 속도  
  하드웨어 속도가 빠르다면 같은 시간에 더 많은 코드를 실행함

#### 게임 시간 vs 실제 시간
게임 개발 초창기에는 두 번째 요인이 정해져 있어서 개발자는 한 틱에 얼마나 많은 작업을 할지만 정해면 됐음  
이 때는 같은 게임을 더 빠른, 혹은 더 느린 기계에서 실행하면 게임 속도도 같이 빨라지거나 느려졌음  
요즘은 개발 중인 게임이 정확히 어느 하드웨어에서 실행될지 알 수 없는 경우가 많음 
따라서, **어떤 하드웨어에서도 일정한 속도로 실행될 수 있도록 하는 것**이 게임 루프의 또 다른 핵심

#### 9.3 패턴
게임 루프는 게임하는 내내 실행됨  
한 번 돌 때마다 멈춤 없이 **유저 입력을 처리**한 뒤 **게임 상태를 업데이트**하고 **게임 화면을 렌더링**  
시간 흐름에 따라 **게임플레이 속도를 조절**

#### 9.4 언제 쓸 것인가?
부적합한 패턴은 안 쓰는 것만 못하기 때문에 적절하게 각 패턴을 사용해야 했음  
하지만, 게임 루프는 무조건 사용 됨  
몇몇 게임은 게임 상태가 진행되지 않아 루프가 필요없어 보이나, 게임의 시각 및 청각 상태는 계속 바뀌어야 함  
유저가 '기다리는' 동안에도 애니메이션과 음악은 계속 돌아가야함  

#### 9.5 주의사항
그래픽 UI와 이벤트 루프가 들어 있는 OS나 플랫폼에서 게임을 만들 경우는 애플리케이션 루프가 두 개 있는 셈이므로 서로를 잘 맞춰야함  

제어권을 가져와 우리 루프만 남겨놓을 수도 있음  
플랫폼에 따라서는 내부 이벤트 루프를 무시하기 어려울 수도 있음  
이럴 경우에는 특정 함수를 호출한 뒤 브라우저가 우리 코드를 콜백으로 호출해주기를 기다려야 함  

#### 9.6 예제 코드  
가장 간단한 게임 루프 형태는 이미 앞에서 살펴봤음  
```
while (true)
{
  processInput();
  update();
  render();
}
```
이 방식은 게임 실행 속도를 제어할 수 없다는 문제가 있음  
빠른 하드웨어에서는 빠르게, 느린 하드웨어에서는 느리게 돌아간다.  
콘텐츠, AI, 물리 계산이 많은 지역이나 레벨이 있다면, 그 부분에서만 느리게 실행될 것  

#### 한숨 돌리기
첫 번째 변형에서는 이 문제를 간단하게 해결  

게임을 60FPS으로 돌린다면 한 프레임에 16ms가 주어짐(1초를 FPS로 나누면 프레임당 초를 구할 수 있음)  
그동안 게임 진행과 렌더링을 다 할 수 잇다면 프레임 레이트를 유지할 수 있음  

다음처럼 프레임을 실행한 뒤에 다음 프레임까지 남은 시간을 **기다리면** 됨  

![image](https://user-images.githubusercontent.com/32252062/72522049-136c2a00-38a0-11ea-98a6-014b2d3741cb.png)
(그림 9-1) 진짜 간단한 게임 루프  

코드는 대강 다음과 같음  
```
while (true)
{
  double start = getCurrentTime();
  processInput();
  update();
  render();

  sleep(start + MS_PER_FRAME - getCurrentTime());
}
```

한 프레임이 빨리 끝나도 sleep() 덕분에 게임이 너무 **빨라지지는** 않음  
다만 너무 **느려지는 건** 막지 **못함**  

그래픽과 AI 수준을 낮춰서 한 프레임에 할 일을 줄이는 식으로 문제를 회피할 수는 있으나  
이러면 모든 유저, 심지어 빠른 하드웨어에서 플레이하는 유저의 게임플레이 품질에도 영향을 미침  

#### 한 번은 짧게, 한 번은 길게
문제는 다음 두 가지  
1. 업데이트 할 때마다 정해진 만큼 게임 시간이 진행됨
2. 업데이트하는 데에는 현실 세계의 시간이 어느 정도 걸림  

2번이 1번보다 오래 걸리면 게임은 느려짐  

게임 시간을 16ms 진행하는 데 걸리는 시간이 16ms보다 더 걸리면 따라갈 수 없음  
하지만 한 번에 게임 시간을 16ms 이상 진행할 수만 있다면, 업데이트 횟수가 적어도 따라갈 수 있음  

즉, 프레임 이후로 실제 시간이 얼마나 지났는지에 따라 시간 간격을 조절하면 됨  
프레임이 오래 걸릴수록 게임 간격을 길게 잡음  
필요에 따라 업데이트 단계를 조절할 수 있기 때문에 실제 시간을 따라갈 수 있음  

이런 걸 **가변** 시간 간격 혹은 **유동** 시간 간격 이라고 함
```
double lastTime = getCurrentTime();
while (true)
{
  double current = getCurrentTime();
  double elapsed = current - lastTime;
  processInput();
  update(elapsed);
  render();
  lastTime = current;
}
```
매 프레임마다 이전 게임 업데이트 후 실제 시간이 얼마나 지났는지를 elapsed에 저장  
게임 상태를 업데이트할 때 elapsed를 같이 넘겨주면 받는 쪽에서는 *지난 시간만큼 게임 월드 상태를 진행*  

게임에서 총알이 날아다닌다고 할 때,  
고정 시간 간격에서는 매 프레임마다 총알 속도에 맞춰서 총알을 움직임  
가변 시간 간격에서는 속도와 지나간 시간을 곱해서 이동 거리를 구함  

시간 간격이 커지면 총알을 더 많이 움직임  
짧고 빠른 간격으로 20번 업데이트하든, 크고 느린 간격으로 4번 업데이트하든 상관없이 총알은 같은 실제 시간 동안 같은 거리를 이동함  

- 다양한 하드웨어에서 비슷한 속도로 게임이 돌아감
- 더 빠른 하드웨어를 사용하는 유저는 더 부드러운 게임플레이를 즐길 수 있음  
가변 시간 간격을 이용하면 전부 해결된거 같이 보임  

하지만 한 가지 심각한 문제가 숨어 있음  
이 방식을 쓰면 게임이 비결정적이자 불안정하게 됨(결정적 : 프로그램을 실행할 때마다 입력이 같으면 결과도 정확하게 같음)  

예를 들면, 2인용 네트워크 게임에서 두 사람의 컴퓨터 사양이 차이가 날 때  
좋은 PC에서는 1초 동안 50프레임을, 좋지 않은 PC에서는 1초에 5프레임을 보여준다고 가정함  

즉, 좋은 PC에서는 물리 엔진이 총알 위치를 50번 업데이트하는 반면  
좋지 않은 PC에서는 5번밖에 업데이트하지 못함  

보통 게임에서는 부동 소수점을 쓰기 때문에 반올림 오차가 생기기 쉬움  
부동소숫점 숫자를 더할 때마다 결과 값에 오차가 생길 수 있다는 말  

좋은 PC에서는 덧셈을 10배 더 많이 하기 때문에 좋지 않은 PC보다 오차가 더 크게 쌓임  

결국 PC에 따라 같은 총알의 위치가 달라짐  

가변 시간 간격에서 생길 수 있는 문제는 이 뿐만 아님  
실시작으로 실행하기 위해 게임 물리 엔진은 실제 물리 법칙의 근사치를 취함  
근사치가 튀는 걸 막기 위해 감쇠를 적용함  

감쇠는 시간 간격에 맞춰 세심하게 조정해야 함  
감쇠 값이 바뀌다보면 물리가 불안해짐  

이런 문제가 있기 때문에 가변 시간 간격보다 나은 방법을 찾아야 함  

#### 따라잡기
가변 시간 간격에 영향을 받지 않는 부분 중 하나가 렌더링  
렌더링은 실행되는 순간을 포착할 뿐, 이전 렌더링 이후로 시간이 어느 정도 지났는지는 고려하지 않고, 때가 되면 렌더링 할 뿐  

이 점을 활용하여 고정 시간 간격으로 업데이트 하되, 렌더링 간격을 유연하게 만들어 프로세스 낭비를 줄일 수 있음  

원리는 이전 루프 이후로 실제 시간이 얼마나 지났는지를 확인한 후, 게임의 '현재'가 실제 시간의 '현재'를 따라잡을 때까지 **고정** 시간 만큼 게임 시간을 **여러 번** 시뮬레이션함

```
double previous = getCurrentTime();
double lag = 0.0;
while (true)
{
  double current = getCurrentTime();
  double elapsed = current - previous;
  previous = current;
  lag += elapsed;

  processInput();

  while (lag >= MS_PER_UPDATE)
  {
    update();
    lag -= MS_PER_UPDATE;
  }

  render();
}
```
프레임을 시작할 때마다 실제 시간이 얼마나 지났는지를 lag 변수에 저장  
이 값은 실제 시간에 비해 게임 시간이 얼마나 뒤쳐졌는지를 의미  
그 다음으로 안에서 고정 시간 간격 방식으로 루프를 돌면서 실제 시간을 따라잡을 때까지 게임을 업데이트함  
다 따라잡은 후에 렌더링하고 다시 루프를 실행  
![image](https://user-images.githubusercontent.com/32252062/73333159-4359fb00-42ac-11ea-870d-7cdc991f4531.png)
(그림 9-2) 핵심 루프로부터 렌더링을 분리  

여기서 시간 간격은 더 이상 시각적 프레임 레이트가 아니라 게임을 얼마나 **촘촘하게** 업데이트할지에 대한 값일 뿐  
시간 간격이 길수록 게임플레이가 끊겨 보임  
하지만 시간 간격이 너무 짧아지지 않도록 주의해야함  
가장 느린 하드웨어에서 update()를 실행하는 데 걸리는 시간보다는 시간 간격이 커야 게임 시간이 계속 뒤쳐지는 일이 없음  

#### 중간에 끼는 경우
위의 방법도 아직 자투리 시간 문제가 남아 있음  
업데이트는 고정 시간 간격이더라도, 렌더링은 그냥 함  
즉, 유저 입장에서는 두 업데이트 사이에 렌더링되는 경우가 종종 발생함  

![image](https://user-images.githubusercontent.com/32252062/73333175-4fde5380-42ac-11ea-8713-b1dd10748e66.png)
(그림 9-3) 시간에 따른 업데이트와 렌더링 시점  
![image](https://user-images.githubusercontent.com/32252062/73333176-51a81700-42ac-11ea-9afd-4da6a9203e07.png)
(그림 9-4) 두 업데이트 사이에 렌더링이 실행됨  

만약에 두 업데이트 사이에 렌더링이 실행된다면,  
총알이 화면 왼쪽에서 오른쪽으로 이동 중일 때, 두 업데이트 중간에 렌더링 하기 때문에 총알은 화면 가운데 있어야하지만, 구현에서 총알은 여전히 화면 왼쪽에 있음
이 결과 움직임이 튀어 보일 수 있음  

다행히 렌더링할 때 업데이트 프레임이 시간적으로 얼마나 떨어져 있는지를 lag 값을 보고 정확하게 알 수 있음  
lag 값이 0이 아니고 업데이트 시간 간격보다 적을 때는 업데이트 루프를 빠져나옴  
이때 lag에 있는 값은 다음 프레임까지 남은 시간  

렌더링할 때는 `render(lag / MS_PER_UPDATE)`를 인수로 넘김  

렌더러는 게임 객체들과 각각의 현재 속도를 알고 있음  
총알이 화면 왼쪽으로부터 20픽셀에 있고, 오른쪽으로 프레임당 400픽셀로 이동한다고 가정하면  
프레임 중간이라면 render()는 0.5를 인수로 받아서 총알의 한 프레임의 중간인 220픽셀에 그리면 움직임이 부드러워짐  

이런 보간은 틀릴 수 있음  
렌더링에서는 현재 프레임에서의 위치와 다음 프레임에서의 예상 위치를 이용해 위치를 보간함  
하지만 물리와 AI를 실제로 업데이트하기 전에는 정확한 위치를 알 수 없음  
다행히 이런 위치 보간은 그다지 눈에 띄지 않음  
보간을 하지 않아서 움직임이 튀는 것보다는 덜 거슬림  

#### 9.7 디자인 결정
화면 재생 빈도와의 동기화, 멀티스레딩, GPU까지 고려하면 실제 게임 루프 코드는 훨씬 복잡해질 수 있음  
고수준에서 확인해야 할 질문들을 보자면  

#### 게임 루프를 직접 관리하는가, 플랫폼이 관리하는가?
기존 게임 엔진을 사용한다면 엔진에 들어있는 게임 루프를 그대로 쓸 가능성이 높음  
- 플랫폼 이벤트 루프 사용
  - 간단함  
    게임 핵심 루프 코드를 작성하고 최적화하느라 고민하지 않아도 됨  
  - 플랫폼에 잘 맞음  
    플랫폼 입력 모델과 우리 쪽 입력 모델이 맞지 않는 부분을 맞춰주느라고 신경 쓰지 않아도 됨
  - 시간을 제어할 수 없음  
    플랫폼은 자기가 적당하다고 생각할 때 우리 코드를 호출함  
    부드럽게 호출되지 않으면 문제가 되지 됨  
    게다가 애플리케이션 이벤트 루프 대부분은 게임을 고려하지 않고 설계했기 때문에 느리고 뚝뚝 끊길 때가 많음
- 게임 엔진 루프 사용
  - 코드를 직접 작성하지 않아도 됨  
  - 코드를 직접 작성할 수 없음  
    엔진 루프에서 아쉬운 게 있어도 건드릴 수 없다는 게 단점
- 직접 만든 루프 사용
  - 완전한 제어  
    뭐든지 할 수 있음
  - 플랫폼과 상호작용해야 함  
    프레임워크에 시간이 주어지지 않을 수 있음, 이럴 땐 프레임워크가 행되지 않도록 가끔 제어권을 넘겨줘야함

#### 전력 소모 문제
요즘은 스마트폰, 노트북, 모바일 게임 시대가 되면서 전력 문제를 고민해야 함  
즉, CPU를 가능한 적게 쓸 방법도 고민해야함  
한 프레임에 해야 할 작업이 끝나면 CPU를 쉬게 해줘야 하는 성능 상한이 있는 셈
- 최대한 빨리 실행하기
  PC 게임이 주로 이렇게 하고 있음  
  게임 루프에서 따로 OS에 sleep을 호출하지 않음  
  대신 시간이 남으면 FPS나 그래픽 품질을 더 높임  

  이 방식은 최고의 게임플레이를 제공하겠지만 전력도 최대한 많이 사용함

- 프레임 레이트 제한하기  
  모바일 게임에서는 그래픽 품질보다는 게임플레이 품질에 더 집중하는 편  
  프레임 레이트에 상한(30FPS 또는 60FPS)을 두는게 보통  
  게임 루프에서 프레임 시간 안에 할 일이 전부 끝났따면 나머지 sleep을 호출

  이 방식은 '충분히 괜찮은' 경험을 제공하면서도, 배터리 소모를 줄일 수 있음

#### 게임플레이 속도는 어떻게 제어할 것인가?
게임 루프에는 비동기 유저 입력과 시간 따라잡기라는 두 가지 핵심요소가 있음  
다양한 플랫폼을 어떻게 지원하느냐가 핵심  
- 동기화 없는 고정 시간 간격 방식
  - 간단함
  - 게임 속도는 하드웨어와 게임 복잡도에 바로 영향을 받음  
    이 방식의 주된 결함은 조그만 차이에도 게임 속도가 달라질 수 있다는 것
- 동기화하는 고정 시간 간격 방식
  - 그래도 간단한 편
  - 전력 효율이 높음
  - 게임이 너무 빨라지진 않음
  - 게임이 너무 느려질 수 있음
- 가변 시간 간격 방식
  - 너무 느리거나 너무 빠른 곳에서도 맞춰서 플레이 할 수 있음
  - 게임플레이를 불안정하고 비결정적으로 만듬
- 업데이트는 고정 시간 간격으로, 렌더링은 가변 시간 간격으로
  '따라잡기' 섹션, 고정 시간 간격으로 업데이트하되, 실제 시간을 따라잡아야 한다면 렌더링 프레임을 낮출 수 있음
  - 너무 느리거나 너무 빨라도 잘 적응함
  - 훨씬 복잡함

---
---
### CHAPTER 10. 업데이트 메서드
---
#### 10.1 의도
객체별로 한 프레임 단위의 작업을 진행하라고 알려줘서 전체를 시뮬레이션함  

게임 루프는 매 프레임마다 객체 컬렉션을 쭉 돌면서 update()를 호출함  
이때 각 객체는 한 프레임만큼 동작을 진행  

#### 10.4 언제 쓸 것인가?
게임이 추상적이거나 게임에서 움직이는 것들이 살아 있다기보단 체스 말에 가깝다면 업데이트 메서드가 맞지 않을 수 있음  

업데이트 메서드는 다음과 같을 때 쓰임  
- 동시에 동작해야 하는 객체나 시스템이 게임에 많음
- 각 객체의 동작은 다른 객체와 거의 독립적
- 객체는 시간의 흐름에 따라 시뮬레이션 되어야 함

#### 10.5 주의사항
- 코드를 한 프레임 단위로 끊어서 실행하는게 더 복잡  
유저 입력, 렌더링 등을 게임 루프가 처리하려면 게임 루프가 매 프레임마다 호출하도록 제어권을 넘겨야함  
그뿐 아니라 동작 코드를 프레임마다 조금씩 실행되도록 쪼개어 넣으려면 코드가 복잡해져 구현 비용이 더 들어감

- 다음 프레임에서 다시 시작할 수 있도록 현재 상태를 저장해야 함  
게임 루프가 매 프레임마다 호출하도록 제어권을 넘기면 코드 반환 이후에 이전 실행 위치를 알 수 없기 때문에 다음 프레임에서도 돌아갈 수 있또록 정보를 충분히 따로 저장해야 함  

  이럴 때 상태 패턴(7장)이 좋을 수 있음

- 모든 객체는 매 프레임마다 시뮬레이션 되지만 진짜로 동시에 되는 건 아님  
게임 루프는 컬렉션을 돌면서 모든 객체를 업데이트함  
update 함수에서는 다른 게임 월드 상태에 접근할 수 있는데, 특히 업데이트 중인 다른 객체에도 접근할 수 있음  
이러다 보니 **객체 업데이트 순서**가 중요  

  객체 목록에서 A가 B보다 앞에 있다면, A는 **B의 이전 프레임 상태**를 봄  
  B차례가 왔을 때 A는 이미 업데이트 했기 때문에 **A의 현재 프레임 상태**를 보게 됨  
  플레이어에게는 모두가 동시에 움직이는 것처럼 보일지 몰라도 내부에서는 순서대로 업데이트 됨  
  다만, 한 프레임 안에 전체를 다 도는 것 뿐  
  (어떤 이유에서건 순차적으로 동작하지 않게 하려면 이중 버퍼 패턴(8장) 같은 게 필요)

  순차적으로 업데이트하면 게임 로직을 작업하기가 편함

- 업데이트 도중에 객체 목록을 바꾸는 건 조심해야 함  
이 패턴에서는 많은 게임 동작이 업데이트 메서드 안에 들어가게 됨  
그중에는 업데이트 가능한 객체를 게임에서 추가, 삭제하는 코드도 포함됨  

  해골 경비병을 죽이면 아이템이 떨어진다고 가정했을 때, 객체가 새로 생기면 보통은 별 문제없이 객체 목록 뒤에 추가하면 됨  
  계속 객체 목록을 순회하다 보면 결국에는 새로 만든 객체까지 도달해 그것까지 업데이트 하게 될 것  

  하지만 이렇게 하면 새로 생선된 객체가 스폰된 걸 플레이어가 볼 틈도 없이 해당 프레임에서 작동하게 됨  
  이게 싫다면 업데이트 루프를 시작하기 전에 목록에 있는 객체 개수를 미리 저장해 놓고 그만큼만 업데이트 하면 됨
  ```
  int numObjectsThisTurn = numObjects_;
  for (int i = 0; i < numObjectsThisTurn; i++)
  {
    objects_[i]->update();
  }
  ```  
  여기서 objects_는 게임에서 업데이트 가능한 객체 배열이고 numObjects_는 객체 개수임  
  객체가 추가되면 numObjects_는 증가하지만 루프 시작 전에 객체 개수를 미리 numObjectsThisTurn에 저장했기 때문에 루프는 이번 프레임에 추가된 객체 앞에서 멈춤  

  순회 도중 객체를 삭제하는 것은 더 어려움  
  만약에 괴물을 죽였다면 객체 목록에서 빼야 함  
  업데이트 하려는 객체 이전에 있는 객체를 삭제할 경우, 의도치 않게 객체 하나를 건너뛸 수 있음
  ```
  for (int i = 0; i < numObjects_; i++)
  {
    objects_[i]->update();
  }
  ```
  ![image](https://user-images.githubusercontent.com/32252062/73335806-b61aa480-42b3-11ea-8777-540d9ac9a623.png)
  (그림 10-1) 순회 도중에 삭제했더니 농부를 건너 뜀  
  위와 같이 괴물이 죽었을 때 괴물이 배열에서 빠지면 농부는 업데이트 되지 않음  

  이를 고려해서 객체를 삭제할 때 순회 변수 i를 업데이트하는 것도 한 방법  

  목록을 다 순회할 때까지 삭제를 늦추는 수도 있음  
  객체에 '죽었음'표시를 하되 그대로 둬서, 업데이트 도중에 죽은 객체를 만나면 그냥 넘어가고, 전체 목록을 다 돌고 나면 다시 목록을 돌면서 죽은 객체를 제거함  

#### 10.6 예제 코드
업데이트 메서드 패턴은 단순함

해골 병사와 석상을 표현할 Entity 클래스
```
class Entity
{
public:
  Entity()
  : x_(0), y_(0)
  {}

  virtual ~Entity() {}
  virtual void update() = 0;

  double x() const { return x_; }
  double y() const { return y_; }

  void setX(double x) { x_ = x; }
  void setY(double y) { y_ = y; }

private:
  double x_;
  double y_;
};
```

게임은 개체 컬렉션을 관리함  
예제에서는 게임 월드를 대표하는 클래스에 개체 컬렉션 관리를 맡김  
(실제라면 컬렉션 클래스를 사용하나, 코드를 단순화하기 위해 배열을 사용)
```
class World
{
public:
  World()
  : numEntities_(0)
  {}

  void gameLoop();

private:
  Entity* entities_[MAX_ENTITIES];
  int numEntities_;
};
```

```
void World::gameLoop()
{
  while (true)
  {
    // 유저 입력 처리

    // 각 개체를 업데이트
    for (int i = 0; i < numEntities_; i++)
    {
      entities_[i]->update();
    }

    // 물리, 렌더링...
  }
}
```
매 프레임마다 개체들을 업데이트하면 업데이트 메서드 구현이 끝남

#### 개체 정의
순찰을 도는 해골 경비병과, 번개를 쏘는 마법 석상을 정의  

```
class Skeleton : public Entity
{
public:
  Skeleton()
  : patrollingLeft_(false)
  {}

  virtual void update()
  {
    if (patrollingLeft_)
    {
      setX(x() - 1);
      if (x() == 0) patrollingLeft_ = false;
    }
    else
    {
      setX(x() + 1);
      if (x() == 100) patrollingLeft_ = true;
    }
  }

private:
  bool patrollingLeft_;
};
```
앞에서 본 게임 루프에 있던 코드를 update 메서드에 거의 그대로 가져왔음  
차이점이라면 지역 변수였던 patrollingLeft_가 멤버 변수 로 바뀐 정도  
이렇게 함으로써 update() 호출 후에도 값을 유지할 수 있음  

```
class Statue : public Entity
{
public:
  Statue(int delay)
  : frames_(0),
    delay_(delay)
  {}

  virtual void update()
  {
    if (++frames_ == delay_)
    {
      shootLightning();

      // 타이머 초기화
      frames_ = 0;
    }
  }

private:
  int frames_;
  int delay_;

  void shootLightning()
  {
    // 번개를 쏨
  }
};
```

명령형 코드에서는 석상마다 프레임 카운터와 발사 주기를 지역 변수로 따로 관리해야 했음  
하지만 이들 변수를 Statue 클래스로 옮겼기 때문에 석상 인스턴스 타이머를 각자 관리할 수 있어서  
석상을 원하는 만큼 많이 만들 수 있음  

이런 게 업데이트 패턴을 활용하는 동기  
객체가 자신이 필요한 모든 걸 직접 들고 관리하기 때문에 게임 월드에 새로운 개체를 추가하기가 훨씬 쉬워짐  

![image](https://user-images.githubusercontent.com/32252062/73336400-1100cb80-42b5-11ea-89b8-aac996fb7b42.png)
(그림 10-2) 위 코드를 UML 클래스 다이어그램으로 만든 것

#### 시간 전달
지금까지는 update()를 부를 때마다 게임 월드 상태가 동일한 고정 단위 시간만큼 진행된다고 가정한 것  

가변 시간 간격을 쓰는 게임도 많음  
가변 시간 간격에서는 게임 루프를 돌 때마다 이전 프레임에서 작업 진행과 렌더링에 걸린 시간에 따라 시간 간격을 크게 혹은 짧게 시뮬레이션함  

즉, 매번 update 함수는 얼마나 많은 시간이 지났는지를 알아야 하기 때문에 지난 시간을 인수로 받음
```
void Skeleton::update(double elapsed)
{
  if (patrollingLeft_)
  {
    x -= elapsed;
    if (x <= 0)
    {
      patrollingLeft_ = false;
      x = -x;
    }
  }
  else
  {
    x += elapsed;
    if (x >= 100)
    {
      patrollingLeft_ = true;
      x = 100 - (x - 100);
    }
  }
}
```
해골 병사의 이동 거리는 지난 시간에 따라 늘어남  
가변 시간 간격을 처리하느라 코드가 좀 더 복잡해짐  
시간 간격이 크면 해골 경비병이 순찰 범위를 벗어날 수 있으므로 주의해야 함

#### 10.7 디자인 결정
이런 단순한 패턴은 변형이 많지 않지만, 조율할 여지는 있음

#### 업데이트 메서드를 어느 클래스에 둘 것인가?
가장 분명하면서도 가장 중요한 결정
- 개체 클래스  
  이미 개체 클레스가 있다면 다른 클래스를 추가하지 않아도 된다는 점에서 **가장 간단한 방법**  

  개체 종류가 많은데 새로운 동작을 만들 때마다 개체 클래스를 상속받아야 한다면 코드가 망가지기 쉽고 작업하기 어려움  
  언젠가 단일 상속 구조로는 코드를 매끄럽게 재사용할 수 없는 순간이 올 텐데, 이러면 방법이 없음  

- 컴포넌트 클래스  
  이미 컴포넌트 패턴(14장)을 쓰고 있따면 더 생각할 것이 없음  
  컴포넌트는 알아서 자기 자신을 업데이트 할 것  

  컴포넌트 패턴은 한 개체의 일부를 개체의 다른 부분들과 디커플링함  
  렌더링, 물리, AI는 스스로 알아서 돌아감

- 위임 클래스  
  상태 패턴(7장)은 상태가 위임하는 객체를 바꿈으로써 객체의 동작을 변경할 수 있게 해 줌  
  타입 객체 패턴(13장)은 같은 '종류'의 여러 개체가 동작을 공유할 수 있게 해줌  

  이들 패턴 중 하나를 쓰고 있따면 위임 클래스에 update()를 두는 게 자연스러움  

  여전히 update() 메서드는 개체 클레스에 있찌만 가상 함수가 아니며 다음과 같이 위임 객체에 포워딩만 함  
  ```
  void Entity::update()
  {
    // 상태 객체에 포워딩한다.
    state_->update();
  }
  ```

  새로운 동작을 정의하고 싶다면 위임 객체를 바꾸면 됨  
  컴포넌트와 마찬가지로, 완전히 새로운 상속 클래스를 정의하지 않아도 동작을 바꿀 수 있는 유연성을 얻을 수 있음


#### 휴면 객체 처리
종종 여러 이유로 일시적으로 업데이트가 필요 없는 객체가 생길 수 있음  
이런 객체가 많으면 매 프레임마다 쓸데없이 객체를 순회하면서 CPU 클럭을 낭비하게 됨 

- 비활성 객체가 포함된 컬렉션 하나만 사용할 경우
  - 시간을 낭비함  
  비활성 객체의 경우 '활성 상태인지' 나타내는 플래그만 검사하거나 아무것도 하지 않는 메서드를 호출할 뿐

- 활성 객체만 모여 있는 컬렉션을 하나 더 둘 경우  

  객체가 비활성화되면 컬렉션에서 제거, 다시 활성화되면 컬렉션에 추가
  - 두 번째 컬렉션을 위해 메모리를 추가 후 사용해야 함  
    전체 개체를 대상으로 작업해야 할 수도 있기 때문에 모든 개체를 모아놓은 마스터 컬렉션도 있기 마련  
    이때 활성 객체 컬렉션을 엄밀히 말하자면 중복 데이터임  
    메모리보다 속도가 중요하다면 그 정도는 감수할만 함

  - 컬렉션 두 개의 동기화를 유지해야 함  
    객체가 생성되거나 완전히 소멸되면 주 컬렉션과 활성 객체 컬렉션을 둘 다 변경해야 함

---
---
## Part 4. 행동 패턴
---
### CHAPTER 11. 바이트코드  
---
#### 11.1 의도
가상 머신 명령어를 인코딩한 데이터로 행동을 표현할 수 있는 유연함 제공

#### 11.2 동기
게임을 만들려면 엄청난 양의 복잡한 소스 코드를 구현해야 함  
콘솔 제조사나 앱 마켓에서 품질을 엄격하게 심사하기 때문에 한 번이라도 크래시가 나면 출시하기 어려움  
동시에 플랫폼의 성능도 최대한 뽑아내야 함  

이러한 환경에서 성능과 안정성을 위해 C++같은 중량 언어를 사용함  
이런 언어는 하드웨어 성능을 최대한 끌어낼 수 있는 저수준 표현과 버그를 막거나 적어도 가둬두기 위한 풍부한 타입 시스템을 함께 제공함  

규모가 큰 게임의 빌드 시간은 엄청나게 오래 걸리기도 함

게임에는 가장 중요한 제약 조건이 한 개 더 있음  
바로 **재미**  
재미 있는 게임을 만들기 위해서는 반복 개발을 계속해야 하는데, 뭐든 살짝만 고치려고 해도 코드 여기저기를 건드려야 하고 느린 빌드를 기다리면 굉장히 비효율적  

#### 마법 전투
두 마법사가 어느 한 쪽이 이길 떄까지 서로에게 마법을 쏘는 대전 게임을 만든다고 가정했을 때  
마법을 코드로 만들어도 되나, 이러면 마법을 고쳐야 할 때마다 프로그래머가 코드를 고쳐야함  
기획자가 수치를 약간 바꿔서 느낌만 보고 싶을 때에도 **게임 전체를 빌드한 뒤에 게임을 다시 실행**해 전투를 해봐야 함  

요즘 게임은 출시한 뒤에도 업데이트를 통해서 버그를 고치거나 콘텐츠를 추가할 수 있어야 하는데,  
마법이 전부 하드코딩되어 있으면, 마법을 바꿀 떄마다 게임 실행 파일을 패치해야 함  

더 나아가 모드를 지원해야 한다고 했을때, 코드로 마법을 구현했다면 모드 개발자는 게임을 빌드하기 위해 컴파일러 툴체인을 다 갖춰야 하고, 개발사는 소스를 공개해야 함  

이렇게 모더가 만든 마법에 버그라도 있따면 다른 플레이어들마저 크래시시킬 수도 있음  

#### 데이터 > 코드
게임 엔진에서 사용하는 개발 언어는 마법을 구현하기에 적합하지 않음  

마법 기능을 핵심 게임 코드와 안전하게 격리할 필요가 있음  

쉽게 고치고, 쉽게 다시 불러올 수 있고, 나머지 게임 실행 파일과는 물리적으로 떼어놓는게 좋음  
이것이 바로 **데이터**  

행동을 데이터 파일에 따로 정의해놓고 게임 코드에서 읽어서 '실행'할 수만 있다면, 앞에서 말한 모든 목표를 달성할 수 있음

파일에 있는 바이트로 행동을 어떻게 표현할 수 있을까?

#### 인터프리터 패턴  
어떤 프로그래밍 언어가 `(1 + 2) * (3 - 4)` 라는 수식을 지원할 때  
이런 표현식을 읽어서 언어 문법에 따라 각각 **객체**로 변환해야 함  
숫자 리터럴은 다음과 같이 각기 객체가 됨
![image](https://user-images.githubusercontent.com/32252062/73426999-1e7f8980-4379-11ea-944e-f83ae1fde324.png)
(그림 11-1) 숫자 리터럴 4개가 순서대로 놓여 있다.

숫자 상수는 단순히 숫자 값을 래핑한 객체임  
연산자도 객체로 바뀌는데, 이때 피연산자도 같이 참조함  
괄호와 우선순위까지 고려하게 되면 표현식이 다음과 같이 작은 객체 트리로 바뀜(파싱, parsing)  
![image](https://user-images.githubusercontent.com/32252062/73427003-20e1e380-4379-11ea-98c6-67c69100a81d.png)
(그림 11-2) 추상 구문 트리로 만든 중첩 표현식

인터프리터 패턴의 목적은 이런 추상 구문 트리를 만드는 데에서 끝나지 않고 이를 실행하는데 있음  

표현식 혹은 하위 표현식 객체로 트리를 만든 뒤에, 진짜 객체지향 방식으로 표현식이 자기 자신을 평가하게 함  

먼저 모든 표현식 객체가 상속받을 상위 인터페이스를 만듬
```
class Expression
{
public:
  virtual ~Expression() {}
  virtual double evaluate() = 0;
};
```

숫자
```
class NumberExpression : public Expression
{
public:
  NumberExpression(double value)
  : value_(value)
  {}

  virtual double evaluate()
  {
    return value_;
  }

private:
  double value_;
};
```
숫자 리터럴 표현식은 단순히 자기 값을 평가함  

덧셈, 곱셈에는 하위표현식이 들어 있기 때문에 좀 더 복잡함
이런 표현식은 자기를 평가하기 전에 먼저 포함된 하위표현식을 재귀적으로 평가함

```
class AdditionExpression : public Expression
{
public:
  AdditionExpression(Expression* left, Expression* right)
  : left_(left),
    right_(right)
  {}

  virtual double evaluate()
  {
    // 피연산자를 실행
    double left = left_->evaluate();
    double right = right_->evaluate();

    // 피연산자를 더함
    return left + right;
  }

private:
  Expression* left_;
  Expression* right_;
};
```

인터프리터 패턴은 아름답고 단순한 패턴이지만 문제도 좀 있음  
작은 객체들로 이루어진 복잡한 프랙털 트리 모양으로 코드가 표현되기 때문에 다음과 같은 단점이 있음
- 코드를 로딩하면서 작은 객체를 엄청 많이 만들고 연결해야함  
- 이들 객체와 객체를 잇는 포인터는 많은 메모리를 소모함 
- 포인터를 따라서 하위표현식에 접근해야 하기 때문에 데이터 캐시에 치명적,  
  동시에 가상 메서드를 호출하는 것은 명령어 캐시에 치명적  

결론적으로 너무 느리고 메모리가 많이 필요하기 때문에 널리 쓰이는 대부분의 프로그래밍 언어는 인터프리터 패턴을 쓰지 않음

#### 가상 기계어
일반적인 게임이 실행될 때 플레이어의 컴퓨터가 C++ 문법 트리구조를 런타임에 순회하진 않음  
대신 미리 컴파일해놓은 기계어를 실행함  

기계어의 장점은 무엇일까?
- 밀도가 높음 : 바이너리 데이터가 연속해서 꽉 차 있어서 한 비트도 낭비하지 않음
- 선형적 : 명령어가 같이 모여 있고 순서대로 실행됨(흐름 제어문 제외), 메모리를 넘나들지 않음
- 저수준 : 각 명령어는 비교적 최소한의 작업만 함, 이를 결합하여 흥미로운 행동을 만들 수 있음
- 빠름 : 앞에서 본 이유 + 하드웨어로 직접 구현이 되어있어 속도가 굉장히 빠름  

이런 장점이 좋아 보이긴 해도, 게임에서 실행되는 기계어를 유저에게 제공하는 것은 해커들에게 좋은 먹잇감이 됨  

따라서 기계어의 성능과 인터프리터 패턴의 안정성 사이에서 **절충**해야 함  

실제 기계어를 읽어서 바로 실행하는 대신 **가상 기계어**를 정의하면 됨  
또한 가상 기계어를 실행하는 간단한 에뮬레이터도 만들면 됨  
가상 기계어는 실제 기계어처럼 밀도가 높고, 선형적이고, 상대적으로 저수준이지만, 동시에 게임에서 완전히 제어하기 때문에 안전하게 격리할 수 있음  

이 에뮬레이터를 **가상 머신(Virtual Machine, VM)** 이라고 부르고, VM이 실행하는 가상 바이너리 기계어는 **바이트코드**라고 부름  

바이트코드는 유연하고, 데이터로 여러 가지를 쉽게 정의할 수 있으며, 인터프리터 패턴 같은 고수준 형식보다 성능도 좋음

#### 11.3 패턴
*명령어 집합*은 실행할 수 있는 **저수준 작업들**을 정의  
*명령어*는 **일련의 바이트**로 인코딩됨  
*가상 머신*은 **중간 값들을 스택에 저장**해가면서 이들 명령어를 하나씩 실행함  
*명령어를 조합*함으로써 **복잡한 고수준 행동을 정의**할 수 있음

#### 11.4 언제 쓸 것인가?
바이트코드 패턴은 복잡하고 쉽게 적용하기도 어려움  

정의할 행동은 많은데 다음과 같은 이유로 게임 구현에 사용한 언어로는 구현하기 어려울 때 바이트코드 패턴을 사용함
- 언어가 **너무 저수준**이라 만드는 데 손이 많이 가거나 오류가 생기기 쉬움
- 컴파일 시간이나 다른 빌드 환경 때문에 **반복 개발하기가 너무 오래 걸림**
- **보안에 취약**함, 정의하려는 행동이 게임을 깨먹지 않게 하고 싶다면 나머지 코드로부터 격리해야 함  

바이트코드는 네이티브 코드보다는 느리므로 **성능이 민감한 곳에는 적합하지 않음**  

#### 11.5 주의사항
**되는 대로 만들지 마라**  
하다못해 바이트코드가 표현할 수 있는 범위라도 꼼꼼히 관리해야 함

#### 프론트엔드
저수준 바이트코드 명령어는 성능 면에서는 뛰어나지만, 사용자가 작성할 만한 게 아님  

일반적으로 사용자가 고수준 형식으로 원하는 행동을 작성하면 어떤 툴이 이를 가상머신이 이해할 수 있는 바이트코드로 변환해 줌  
이 툴이 바로 **컴파일러**  

저작 툴을 만들 여유가 없다면 바이트코드 패턴을 쓰기 어려움  
생각보다는 저작 툴 만드는 것이 어렵지 않을 것

#### 디버거
자신이 만든 바이트코드 VM에서는 디버거, 정적 분석기, 디컴파일러 같은 툴은 무용지물임  

디버거에서 VM에 브레이크포인터를 걸고 들여다볼 수는 있지만, VM 그 자체가 무엇을 하는지 알 수 있을 뿐 VM이 바이트코드를 어디까지 해석해 실행 중인지는 알 수 없음  
이런 식으로는 바이트코드가 컴파일되기 전인 고수준 형태를 추적할 수 없음  

정의하려는 행동이 단순하다면 디버깅 툴 없이도 어떻게든 해나갈 수 있지만,  
콘텐츠 규모가 커진다면 바이트코드가 뭐 하는지 확인할 수 있는 기능을 개발하는데 시간을 투자해야함  

이런 기능이 반드시 게임에 포함되어 출시되는 건 아니지만, 게임 출시에 굉장히 중요한 역할 하는 것은 분명  
#### 11.6 예제 코드
먼저 VM에 필요한 명령어 집합을 정의  

#### 마법의 API
마법은 대개 마법사의 스탯 중 하나를 바꿈  
```
void setHealth(int wizard, int amount);
void setWisdom(int wizard, int amount);
void setAgility(int wizard, int amount);
```
첫 번째 매개변수(wizard)는 마법을 적용할 대상  
0이면 우리 마법사, 1이면 상대방 마법사  

회복 마법으로 우리 마법사의 체력은 회복하고 상대방 마법사의 체력은 깍을 수 있음  
간단한 세 가지 함수만으로도 다양한 마법 효과를 만들 수 있음  

마법이 조용히 스탯만 바꾼다면 밋밋하니 효과를 추가  
```
void playSound(int soundId);
void spawnParticles(int particleType);
```

사운드를 재생하고 파티클을 보여주는 이들 함수는 게임플레이에는 영향을 미치지 않지만 긴장감을 높여 줌  

#### 마법 명령어 집합
이들 API가 데이터에서 제어 가능한 뭔가로 어떻게 바뀌는지를 보면 다음과 같음

우선 매개변수부터 전부 제거  
SET___() 같은 함수는 우리 마법사의 스탯을 항상 최대값으로 만듦  
이펙트 효과 역시 하드코딩된 한 줄짜리 사운드와 파티클 이펙트만 보여줌  

이제 마법은 단순하 명령어 집합이 됨  
명령어는 각각 어떤 작업을 하려는지를 나타냄  
명령어들은 다음과 같이 열거형으로 표현할 수 있음

```
enum Instruction
{
  INST_SET_HEALTH      = 0x00,
  INST_SET_WISDOM      = 0x01,
  INST_SET_AGILITY     = 0x02,
  INST_PLAY_SOUND      = 0x03,
  INST_SPAWN_PARTICLES = 0x04
};
```

마법을 데이터로 인코딩하려면 이들 열거형 값을 배열에 저장하면 됨  

원시명령(primitive)이 몇 개 없다 보니 한 바이트로 전체 열거형 값을 다 표현할 수 있음  
마법을 만들기 위한 코드가 실제로는 바이트들의 목록이다 보니 **'바이트코드'** 라고 불림  
![image](https://user-images.githubusercontent.com/32252062/73428543-e1b59180-437c-11ea-8824-140c05b5294f.png)

명령 하나를 실행하려면 어떤 원시명령인지를 보고 이에 맞는 API 메서드를 호출하면 됨  
```
switch (instruction)
{
  case INST_SET_HEALTH:
    setHealth(0, 100);
    break;

  case INST_SET_WISDOM:
    setWisdom(0, 100);
    break;

  case INST_SET_AGILITY:
    setAgility(0, 100);
    break;

  case INST_PLAY_SOUND:
    playSound(SOUND_BANG);
    break;

  case INST_SPAWN_PARTICLES:
    spawnParticles(PARTICLE_FLAME);
    break;
}
```
이런 식으로 인터프리터는 코드와 데이터를 연결함  

마법 전체를 실행하는 VM에서는 이 코드를 다음과 같이 래핑함  
```
class VM
{
public:
  void interpret(char bytecode[], int size)
  {
    for (int i = 0; i < size; i++)
    {
      char instruction = bytecode[i];
      switch (instruction)
      {
        // 각 명령별로 case문이 들어감...
      }
    }
  }
};
```

하지만 이 가상 머신은 전혀 유연하지 않음  
상대방 마법사를 건드리거나 스탯을 낮추는 마법도 만들 수 없음  
사운드도 하나만 출력이 가능함  

실제 언어와 같은 표현력을 갖추려면 **매개변수**를 받을 수 있어야함  

#### 스택 머신
복잡한 중첩식을 실행하려면 가장 안쪽 하위표현식부터 계산해, 그 결과를 이를 담고 있던 표현식의 인수로 넘김  
이걸 전체 표현식이 다 계산될 때까지 반복하면 됨  

인터프리터 패턴에서는 *중첩 객체 트리* 형태로 중첩식을 직접 표현했음  
속도를 높이기 위해 명령어를 1차원으로 나열해도  
하위표현식 결과를 중첩 순서에 맞게 다음 표현식에 전달해야 함  

이를 위해 CPU처럼 **스택을 이용**하여 명령어 실행 순서를 제어함
```
class VM
{
public:
  VM()
  : stackSize_(0)
  {}

  // Other stuff...

private:
  static const int MAX_STACK = 128;
  int stackSize_;
  int stack_[MAX_STACK];
};
```
VM 클래스에는 값 스택이 들어잇음  

명령어들은 이 스택을 통해서 데이터를 주고 받음
```
class VM
{
private:
  void push(int value)
  {
    // 스택 오버플로를 검사
    assert(stackSize_ < MAX_STACK);
    stack_[stackSize_++] = value;
  }

  int pop()
  {
    // 스택이 비어 있지 않은지 확인
    assert(stackSize_ > 0);
    return stack_[--stackSize_];
  }

  // Other stuff...
};
```

명령어가 매개변수를 받을 때는 다음과 같이 스택에서 꺼내옴
```
switch (instruction)
{
  case INST_SET_HEALTH:
  {
    int amount = pop();
    int wizard = pop();
    setHealth(wizard, amount);
    break;
  }

  case INST_SET_WISDOM:
  case INST_SET_AGILITY:
    // 위와 같은 식으로...

  case INST_PLAY_SOUND:
    playSound(pop());
    break;

  case INST_SPAWN_PARTICLES:
    spawnParticles(pop());
    break;
}
```

스택에서 값을 얻어오려면 리터럴 명령어가 필요함  
리터럴 명령어는 정수 값을 나타냄  

명령어 목록이 바이트의 나열이라는 점을 활용해  
숫자를 바이트 배열에 직접 넣으면 리터럴 명령어 자신의 값을 구하는 것과 무한 회귀 문제를 해결할 수 있음  
숫자 리터럴을 위한 명령어 타입을 다음과 같이 정의
```
case INST_LITERAL:
{
  // 바이트코드에서 다음 바이트 값을 읽음
  int value = bytecode[++i];
  push(value);
  break;
}
```

![image](https://user-images.githubusercontent.com/32252062/73429616-73260300-437f-11ea-89f9-c2d43ef5d0b5.png)
(그림 11-3) 바이트코드에 들어 있는 리터럴

인터프리터가 명령어 몇 개를 실행하는 과정  

스택이 비어 있는 상태에서 인터프리터가 첫 번째 명령을 실행  
![image](https://user-images.githubusercontent.com/32252062/73429761-be401600-437f-11ea-9714-85ef777e0a02.png)
(그림 11-4) 이무런 명령을 실행하지 않은 상태

먼저 INST_LITERAL부터 실행함, 이 명령은 자신의 바이트코드 바로 옆 바이트 값(0)을 읽어서 스택에 넣음  
![image](https://user-images.githubusercontent.com/32252062/73429767-c13b0680-437f-11ea-8459-0317ba96e95c.png)
(그림 11-5) 첫 번째 리터럴 명령 실행 후

두 번째 INST_LITERAL을 실행, 10을 읽어서 스택에 넣음  
![image](https://user-images.githubusercontent.com/32252062/73429771-c304ca00-437f-11ea-8833-ba6d22ac66cc.png)
(그림 11-6) 마지막 명령을 실행하기 전

마지막으로 INST_SET_HEALTH를 실행  
스택에서 10을 꺼내와 amount 변수에 넣고, 두 번째로 0을 스택에서 꺼내 wizard 매개변수에 넣어 setHealth 함수를 호출  

이런 방식으로 어느 편 마법사든 스탯을 마음대로 바꿀 수 있는 유연함을 가지게 됨  
다른 사운드나 파티클도 출력할 수 있음  

하지만 여전히 코드보다는 **데이터** 같아 보임  
예를 들어 체력을 지혜 스탯의 반만큼 회복하는 식으로는 만들 수 없음  
기획자는 숫자만이 아니라 **규칙**으로 마법을 표현할 수 있기를 원함  

#### 행동 = 조합

---
> 본 내용은 한빛미디어에서 출판한 '게임 프로그래밍 패턴 : 더 빠르고 깔끔한 게임 코드를 구현하는 13가지 디자인 패턴' 을 읽고 공부하며 작성하였습니다.