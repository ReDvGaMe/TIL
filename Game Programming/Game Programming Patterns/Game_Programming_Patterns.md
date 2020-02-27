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
    - [하위 클래스 샌드박스](#CHAPTER-12.-하위-클래스-샌드박스)
    - [타입 객체](#CHAPTER-13.-타입-객체)
5. [디커플링 패턴](#Part-5.-디커플링-패턴)
    - [컴포넌트](#CHAPTER-14.-컴포넌트)
    - [이벤트 큐](#CHAPTER-15.-이벤트-큐)
    - [서비스 중개자](#CHAPTER-16.-서비스-중개자)
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
---
### CHAPTER 12. 하위 클래스 샌드박스  
---
#### 12.1, 12.2 의도, 동기
상위 클래스가 제공하는 기능들을 통해서 하위 클래스에서 행동을 정의  

슈퍼히어로 게임을 만들고, 수십 개가 넘는 다양한 초능력을 선택한다고 할 때  
Superpower라는 상위 클래스를 만든 후 초능력별로 이를 상속받는 클래스를 정의하려함  
구현을 마치면 수십 개가 넘는 초능력 클래스가 만들어질 것  
(하위 클래스가 많으면 하드코딩보다 데이터 기반으로 구현하는게 나을 수 있음)

Superpower를 상속받은 초능력 클래스에서 사운드, 시각 이펙트. AI와의 상호작용, 다른 게임 개체의 생성과 파괴, 물리 작용 같은 모든 일을 할 수 있다고 할 때,  
초능력 클래스는 온갖 코드를 건드리게 되는데, 이런 식으로 초능력 클래스를 구현하기 시작하면 다음과 같은 일이 발생함  

- 중복 코드가 많아짐  
  여러 부분이 겹칠 가능성이 높음(시각 이펙트, 사운드 등)  
  비슷한 초능력은 서로 비슷한 코드가 나올 것  
  프로그래머들끼리 조율 없이 구현한다면 많은 중복 코드가 생겨날 것

- 거의 모든 게임 코드가 초능력 클래스와 커플링됨  
  초능력 클래스와 직접 엮일 의도가 없었던 하부시스템을 바로 호출하도록 코드를 짜버리면,  
  초능력 클래스가 모든 렌더러 계층에 중구난방으로 접근하게 해놨을 가능성이 높음  

- 외부 시스템이 변경되면 초능력 클래스가 깨질 가능성이 높음  
  게임 내 다양한 코드와 커플링되다 보니 이런 코드가 변경될 때 초능력 클래스에도 영향을 끼침  

- 모든 초능력 클래스가 지켜야 할 불변식을 정의하기 어려움  
  초능력 클래스가 재생하는 모든 사운드를 항상 큐를 통해 적절히 우선순위를 맞추고 싶다고 할 때,  
  수백 개가 넘는 초능력 클래스가 사운드 엔진에 직접 접근한다면 이를 강제하기 쉽지 않음  

이런 일들을 방지하기 위해 원시명령 집합을 제공하는게 좋음  
사운드를 출력하고 싶다면 playSound 함수를, 파티클을 보여주고 싶다면 spawnParticles 함수를 호출하면 됨  
초능력을 구현하는데 필요한 모든 기능을 원시명령이 제공하게 되면 초능력 클래스가 이런저런 헤드를 include하거나, 다른 코드를 찾아 헤메지 않아도됨  

원시명령을 protected로 만드는 이유는 이들 함수가 하위 클래스용이라는 걸 알려주기 위해서  
원시명령을 준비하고 나면, 이를 사용할 공간을 제공해야함  
이를 위해 하위 클래스가 구현해야하 하는 **샌드박스 메서드**를 순수 가상 메서드로 만들어 protected에 둠  

위를 적용하려면 다음과 같이 초능력 클래스를 구현하면 됨  
1. Superpower를 상속받는 새로운 클래스를 만듬
2. 샌드박스 메서드인 activate()를 오버라이드
3. Superpower클래스가 제공하는 protected 메서드를 호출하여 activate()를 구현  

이렇게 상위 클래스가 제공하는 기능을 최대한 고수준 형태로 만듦으로써 중복 코드 문제를 해결할 수 있음  
커플링 문제는 커플링을 한곳으로 몰아서 해결함  
Superpower 클래스는 여러 게임 시스템과 커플링되지만, 수많은 하위 시스템은 다른 코드와는 커플링되지 않음  
게임 시스템이 변경될 때 Superpower 클래스를 고치는 건 피할 수 없지만, 나머지 많은 하위 클래스는 손대지 않아도 됨  

하위 클래스 샌드박스 패턴을 쓰면 클래스 상속 구조가 얇게 펴짐  

#### 12.3 패턴  
**상위 클래스**는 **추상 샌드박스 메서드**와 **여러 제공 기능**을 정의함  
**제공기능**은 **protected**로 만들어져 **하위 클래스용**이라는 걸 분명히 함  
**각 하위 클래스**는 제공 기능을 이용해 샌드박스 메서드를 구현  

#### 12.4 언제 쓸 것인가?
하위 클래스 샌드박스 패턴은 굉장히 단순하고 일반적이라 게임이 아닌 곳에서도 알게 모르게 많이 사용하고 있음  
클래스에 protected인 비-가상 함수가 있다면 이 패턴을 쓰고 있을 가능성이 높음  
하위 클래스 샌드박스 패턴은 이럴 때 좋음  
- 클래스 하나에 하위 클래스가 많이 있음
- 상위 클래스는 하위 클래스가 필요로 하는 기능을 전부 제공할 수 있음
- 하위 클래스 행동 중에 겹치는 게 많아, 이를 하위 클래스끼리 쉽게 공유하고 싶음
- 하위 클래스들 사이의 커플링 및 하위 클래스와 나머지 코드와의 커플링을 최소화하고 싶음

#### 12.5 주의사항
상위 클래스에 코드가 계속 쌓이는 경향이 있어서 상속을 나쁘게 생각함  

하위 클래스는 상위 클래스를 통해서 나머지 게임 코드에 접근하기 때문에 상위 클래스가 하위 클래스에서 접근해야 하는 모든 시스템과 커플링됨  
하위 클래스 역시 상위 클래스와 밀접하게 묶이게 됨  
이런 커플링 관계에서는 상위 클래스를 조금만 바꿔도 어딘가 깨지기 쉬움  
소위 '깨지기 쉬운 상위 클래스'문제에 빠지게 됨  

반대로 좋은 점은 커플링 대부분이 상위 클래스에 몰려 있기 때문에 하위 클래스를 나머지 코드와 깔끔하게 분리할 수 있다는 것  
즉, 많은 코드가 격리되어 있어 유지보수 하기 쉬움  

그럼에도, 상위 클래스 코드가 거대한 스파게티 덩어리가 되어간다면 제공 기능 일부를 별도 클래스로 뽑아내 책임을 나눠 갖게 할 수도 있음  
(14장 컴포넌트 패턴이 도움이 될 것)

#### 12.6 예제 코드
Superpower 상위 클래스
```
class Superpower
{
public:
  virtual ~Superpower() {}

protected:
  virtual void activate() = 0;

  void move(double x, double y, double z)
  {
    // Code here...
  }

  void playSound(SoundId sound, double volume)
  {
    // Code here...
  }

  void spawnParticles(ParticleType type, int count)
  {
    // Code here...
  }
};
```

activate()는 샌드박스 메서드  
순수 가상 함수로 만들었기 때문에 하위 클래스는 반드시 오버라이드 해야 함  
덕분에 초능력 클래스를 구현하려는 개발자는 어디에 작업을 해야 할지를 분명히 알 수 있음  

나머지 protected 메서드인 move, playSound, spawnParticles는 제공 기능  
하위 클래스에서 activate 메서드를 구현할 때 호출함  

```
class SkyLaunch : public Superpower
{
protected:
  virtual void activate()
  {
    // 하늘로 높이 뛰어오른다.
    playSound(SOUND_SPROING, 1.0f);
    spawnParticles(PARTICLE_DUST, 10);
    move(0, 0, 20);
  }
};
```
점프 능력은 하늘 높이 뛰어오르게 할 수 있음  
모든 초능력 클래스 코드가 단순히 사운드, 파티클 이펙트, 모션 조합으로 되어 있다면 하위 클래스 샌드박스 패턴을 쓸 필요가 없음  
대신 초능력 클래스에서는 정해진 동작만 하도록 activate()를 구현해놓고, 초능력별로 다른 사운드 ID, 파티클 타입, 움직임을 사용하게 만들면 됨  
하지만 이런건 모든 초능력이 본질적으로 동작은 같으면서 데이터가 다를 때만 가능

```
class Superpower
{
protected:
  double getHeroX()
  {
    // Code here...
  }

  double getHeroY()
  {
    // Code here...
  }

  double getHeroZ()
  {
    // Code here...
  }

  // Existing stuff...
};
```
히어로 위치를 얻을 수 있는 메서드를 추가  
이제 SkyLaunch 클래스에서 이들 메서드를 사용할 수 있음

```
class SkyLaunch : public Superpower
{
protected:
  virtual void activate()
  {
    if (getHeroZ() == 0)
    {
      // 땅이라면 공중으로 뜀
      playSound(SOUND_SPROING, 1.0f);
      spawnParticles(PARTICLE_DUST, 10);
      move(0, 0, 20);
    }
    else if (getHeroZ() < 10.0f)
    {
      // 거의 땅에 도착했다면 이중 점프
      playSound(SOUND_SWOOP, 1.0f);
      move(0, 0, getHeroZ() - 20);
    }
    else
    {
      // 공중에 높이 떠 있다면 내려찍기 공격
      playSound(SOUND_DIVE, 0.7f);
      spawnParticles(PARTICLE_SPARKLES, 1);
      move(0, 0, -getHeroZ());
    }
  }
};
```
어떤 상태에 대해 접근할 수 있게 만들었기 때문에 샌드박스 메서드에서 실제적이로 흥미로운 제어 흐름을 만들 수 있게 됨  
예제는 간단한 if문 몇 개만 사용했지만, 샌드박스 메서드에는 아무 코드나 넣을 수 있기 때문에 마음대로 구현이 가능  

#### 12.7 디자인 결정  
하위 클래스 샌드박스 패턴은 상당히 '부드러운' 패턴  
이런 패턴은 기본 아이디어는 제공하되 구체적인 부분은 많이 언급하지 않음  
즉, 패턴을 적용할 때마다 흥미로운 선택거리가 있다는 뜻  

-  어떤 기능을 제공해야 하나?  
가장 중요한 질문  
이걸 어떻게 하느냐에 따라 패턴의 느낌이나 사용성이 전혀 달라짐  

  - 제공 기능을 **몇 안 되는 하위 클래스에서만 사용한다면 별 이득이 없음**  
    모든 하위 클래스가 영향을 받는 상위 클래스의 복잡도는 증가하는 반면, 혜택을 받는 클래스는 몇 안 되기 때문  

    다른 제공 기능과 일관성을 유지하는 것도 의미는 있겠지만, 이들 특수한 하위 클래스에서 외부 시스템에 **직접 접근하는 것이 더 간단하고 명확할 수 있음**

  - 다른 시스템의 함수를 호출할 때에도 그 함수가 상태를 변경하지 않는다면 크게 문제가 되지 않음  
    커플링을 생기겠지만, 게임 내에서 다른 걸 망가뜨리지 않는다는 점에서 '안전한' 커플링임  

    외부 시스템의 **상태를 변경하는 함수를 호출**한다면 그 시스템과 더 강하게 결합된다는 점을 좀 더 분명히 인지해야 함  
    이런 것들은 눈에 더 잘들어오는 **상위 클래스의 제공 기능으로** 옮겨주는 게 나을 수 있음

  - 제공 기능이 단순히 외부 시스템으로 호출을 넘겨주는 일밖에 하지 않는다면 있어봐야 좋을 게 없음  
    그럴 때는 하위 클래스에서 외부 메서드를 직접 호출하는 게 더 깔끔할 수 있음

    다만, 단순히 포워딩만 하는 메서드도 하위 클래스에 특정 상태를 숨길 수 있다는 장점이 있음  
    Superpower 클래스에 다음과 같은 제공 기능이 있다고 해보자면
    ```
    void playSound(SoundId sound, double volume)
    {
      soundEngine_.play(sound, volume);
    }
    ```
    playSound 함수는 Superpower의 멤버 변수인 soundEngine_의 함수로 포워딩 할 뿐  
    그럼에도 soundEngine_을 하위 클래스에서 함부로 접근할 수 없도록 캡슐화한다는 장점이 있음

- 메서드를 직접 제공할 것인가? 이를 담고 있는 객체를 통해 제공할 것인가?  
  하위 클래스 샌드박스 패턴의 골칫거리 하나는 상위 클래스의 메서드 수가 끔찍하게 늘어난다는 점  
  이들 메서드 일부를 다른 클래스로 옮기면 이런 문제를 완화할 수 있음  
  상위 클래스의 제공 기능에서는 이들 객체를 반환하기만 하면 됨  

  예를 들어 초능력을 쓸 때 사운드를 내기 위해 Superpwer 클래스에 메서드를 직접 추가할 수 있음
  ```
  class Superpower
  {
  protected:
    void playSound(SoundId sound, double volume)
    {
      // Code here...
    }

    void stopSound(SoundId sound)
    {
      // Code here...
    }

    void setVolume(SoundId sound)
    {
      // Code here...
    }

    // 샌드박스메서드와 그 외 다른 기능들...
  };
  ```

  하지만 Superpower 클래스가 이미 크고 복잡하다면 메서드를 이렇게 추가하고 싶진 않을 것  
  대신 사운드 기능을 제공하는 SoundPlayer 클래스를 만듦
  ```
  class SoundPlayer
  {
    void playSound(SoundId sound, double volume)
    {
      // Code here...
    }

    void stopSound(SoundId sound)
    {
      // Code here...
    }

    void setVolume(SoundId sound)
    {
      // Code here...
    }
  };
  ```

  다음으로 Superpower가 SoundPlayer 객체에 접근할 수 있게 함
  ```
  class Superpower
  {
  protected:
    SoundPlayer& getSoundPlayer()
    {
      return soundPlayer_;
    }

    // 샌드박스 메서드와 그 외 다른 기능들...

  private:
    SoundPlayer soundPlayer_;
  };
  ```

  이런 식으로 제공 기능을 보조 클래스로 옮겨놓으면 다음과 같은 이점이 있음
  - 상위 클래스의 메서드 개수를 줄일 수 있음  
    예제에서는 메서드 세 개를 게터(getter) 하나로 줄임

  - 보조 클래스에 있는 코드가 유지보수하기 더 쉬운 편  
    Superpower 같은 핵심 상위 클래스는 자기를 의존하는 코드가 많다보니 아무리 조심해도 변경하기 쉽지 않음  
    상위 클래스의 기능 일부를 커플링이 적은 보조 클래스로 옮기면 기능을 망치지 않고도 쉽게 고칠 수 있음

  - 상위 클래스와 다른 시스템과의 커플링을 낮출 수 있음  
    playSound()를 Superpower 클래스 메서드에 만들면, Superpower 클래스는 SoundID와 그 외 구현이 호출하는 오디오 코드와 직접 결합됨  
    이를 SoundPlayer 클래스로 옮기면 사운드와 관련된 모든 의존 관계를 SoundPlayer 클래스 하나에 전부 캡슐화 할 수 있음

- 상위 클래스는 필요한 객체를 어떻게 얻는가?  
  상위 클래스 멤버 변수 중에는 캡슐화하고 하위 클래스로부터 숨기고 싶은 데이터가 있을 수 있음  
  처음 본 예제에서 Superpower 클래스의 제공 기능 중에 spawnParticles()가 있었음  
  이 함수를 구현하기 위해서 파티클 시스템 객체가 필요하다면 어떻게 얻을 수 있을까?

- 상위 클래스의 생성자로 받기  
  상위 클래스의 생성자 인수로 받으면 가장 간단함
  ```
  class Superpower
  {
  public:
    Superpower(ParticleSystem* particles)
    : particles_(particles)
    {}

    // 샌드박스 메서드와 그 외 다른 기능들...

  private:
    ParticleSystem* particles_;    
  };
  ```

  이제 모든 초능력 클래스는 생성될 때 파티클 시스템 객체를 참조하도록 강제할 수 있음  
  하지만 하위 클래스의 경우
  ```
  class SkyLaunch : public Superpower
  {
  public:
    SkyLaunch(ParticleSystem* particles)
    : Superpower(particles)
    {}
  };
  ```
  모든 하위 클래스 생성자는 파티클 시스템을 인수로 받아서 상위 클래스 생성자에 전달해야 함  
  원치 않게 모든 하위 클래스에 상위 클래스의 상태가 노출됨  

  상위 클래스에 다른 상태를 추가하려면 하위 클래스 생성자도 해당 상태를 전달하도록 전부 바꿔야 하기 때문에 유지보수하기에도 좋지 않음  

- 2단계 초기화  
  초기화를 2단계로 나누면 생성자로 모든 상태를 전달하는 번거로움을 피할 수 있음  
  생성자는 매개변수를 받지 않고 그냥 객체를 생성, 그 후에 상위 클래스 메서드를 따로 실행해 필요한 데이터를 제공  

  ```
  Superpower* power = new SkyLaunch();
  power->init(particles);
  ```

  SkyLaunch 클래스 생성자에는 인수가 없기 때문에 Superpower 클래스가 private으로 숨겨놓은 멤버 변수와 전혀 커플링되지 않음  
  단, 까먹지 말고 init()을 호출해야 한다는 문제가 있음  
  이걸 빼먹으면 초능력 인스턴스의 상태가 완전치 않아 제대로 작동하지 않을 수 있음  

  이런 문제는 객체 생성 과정 전체를 한 함수로 캡슐화하면 해결 가능
  ```
  Superpower* createSkyLaunch(ParticleSystem* particles)
  {
    Superpower* power = new SkyLaunch();
    power->init(particles);
    return power;
  }
  ```

- 정적 객체로 만들기  
  앞에서는 초능력 인스턴스별로 파티클 시스템 초기화를 했음  
  모든 초능력 인스턴스가 별도의 파티클 객체를 필요로 한다면 말이 되나, 파티클 시스템이 싱글턴이라면 어차피 모든 초능력 인스턴스가 같은 상태를 공유할 것  

  이럴 때는 상위 클래스의 private 정적 멤버 변수로 만들 수 있음  
  여전히 초기화는 필요하지만 인스턴스마다 하지 않고 초능력 클래스에서 한 번만 초기화하면 됨  

  ```
  class Superpower
  {
  public:
    static void init(ParticleSystem* particles)
    {
      particles_ = particles;
    }

    // 샌드박스 메서드와 그 외 다른 기능들...

  private:
    static ParticleSystem* particles_;
  };
  ```

  여기에서 init()과 paricles_은 모두 정적  
  Superpower::init()을 미리 한 번 호출 해놓으면 모든 초능력 인스턴스에서 같은 파티클 시스템에 접근할 수 있음  
  하위 클래스 생성자만 호출하면 Superpower 인스턴스를 그냥 만들 수 있음  

  particles_가 정적 변수이기 때문에 초능력 인스턴스별로 파티클 객체를 따로 저장하지 않아 메모리 사용량을 줄일 수 있다는 것도 장점  

- 서비스 중개자를 이용
  앞에서는 상위 클래스가 필요로 하는 객ㅊ체를 먼저 넣어주는 작업을 밖에서 잊지 말고 해줘야 했음  
  즉, 초기화 부담을 외부 코드로 넘기고 있음  
  만약 상위 클래스가 원하는 객체를 직접 가져올 수 있다면 스스로 초기화 할 수 있음  
  이런 방법 중의 하나가 서비스 중개자 패턴(16장)  

  ```
  class Superpower
  {
  protected:
    void spawnParticles(ParticleType type, int count)
    {
      ParticleSystem& particles = Locator::getParticles();
      particles.spawn(type, count);
    }

    // 샌드박스 메서드와 그 외 다른 기능들...
  };
  ```
  여기서 spawnParicles()는 필요로 하는 파티클 시스템 객체를 이부 코드에서 전달받지 않고 직접 서비스 중개자(Locator 클래스)에서 가져옴

---
---
### CHAPTER 13. 타입 객체
---
#### 13.1, 13.2 의도, 동기
클래스 하나를 인스턴스별로 다른 객체형으로 표현할 수 있게 만들어, 새로운 '클래스들'을 유연하게 만들 수 있게 함  

RPG에서 몬스터를 구현한다고 할 때, 몬스터는 체력, 공격, 그래픽 리소스, 사운드 등 다양한 속성이 있으나, 이번 예제에서는 체력과 공격 속성만 고려하도록 함  

모든 몬스터에는 체력 값이 있음  
체력은 최대 체력에서 시작해서 피해를 입을 때마다 조금씩 줄어들음  
몬스터에는 공격 문구 속성도 있음  
몬스터가 영웅을 공격할 때, 이 공격 문구는 유저에게 어떤 식으로든 표시됨  

기획자는 '용'이나 '트롤'같이 몬스터 종족을 다양하게 만들고 싶어함  
각 종족은 몬스터의 특징을 나타내고, 던전에는 같은 종족 몬스터가 여러 마리 동시에 돌아다닐 수 있음  

종족은 몬스터의 최대 체력을 결정함  
용은 트롤보다 시작 체력이 높아서 죽이기가 훨씬 어려움  
종족은 공격 문구도 결정함  
종족이 같은 몬스터는 공격하는 방식도 모두 같음  

#### 전형적인 OOP 방식
위와 같은 기획을 염두에 두고 코드를 만들 때,  
기획서에 따르면 용, 트롤 등은 모두 몬스터의 일종  
이럴 때 객체지향 방식에서는 Monster라는 상위 클래스를 만드는게 자연스러움  

```
class Monster
{
public:
  virtual ~Monster() {}
  virtual const char* getAttack() = 0;

protected:
  Monster(int startingHealth)
  : health_(startingHealth)
  {}

private:
  int health_; // Current health.
};
```

public에 있는 getAttack()은 몬스터가 영웅을 공격할 때 보여줄 문구를 반환  
하위 클래스는 이 함수르 오버라이드해서 다른 공격 문구를 보여줌  

생성자는 protected이고 몬스터의 최대 체력을 인수로 받음  
각각의 종족을 정희한 하위 클래스에서 public 생성자를 정의해 상위 클래스의 생성자를 호출하면서 종족에 맞는 최대 체력을 인수로 전달함  

종족을 표현한 하위 클래스
```
class Dragon : public Monster
{
public:
  Dragon() : Monster(230) {}

  virtual const char* getAttack()
  {
    return "용이 불을 뿜습니다!";
  }
};

class Troll : public Monster
{
public:
  Troll() : Monster(48) {}

  virtual const char* getAttack()
  {
    return "트롤이 당신을 곤봉으로 내려칩니다!";
  }
};
```

Monster의 하위 클래스는 몬스터의 최대 체력을 전달하고, getAttack()을 오버라이드해서 종족에 맞는 공격 문구를 반환  

이런 식으로 몬스터 클래스를 만들다 보면 어느 순간에 Monster 하위 클래스가 굉장히 많아져 있을 것  

이쯤 되면 작업이 이상하게 느려짐  
프로그래머들은 몇 줄 안되는 Monster 하위 클래스를 계속 작성한 후 컴파일 해야 함  
이런 상황은 기획자들이 이미 만들어놓은 종족을 다듬으려 할 때 더 나빠짐  

종족 상태 값은 게임 코드를 빌드하지 않고도 변경할 수 있어야함  
더 나아가 프로그래머 도움 없이 기획자가 새로운 종족을 만들고 값을 수정할 수 있어야함  

#### 클래스를 위한 클래스
게임에 스폰된 모든 몬스터 인스턴스의 타입은 몬스터 클래스를 상속받음  
종족이 많아질 수록 클래스 상속 구조도 커짐  
종족을 늘릴 때마다 코드를 추가하고 컴파일해야 하는 문제도 있음  

몬스터마다 종족에 대한 정보를 두는 방법도 있음  
종족마다 Monster 클래스를 상속받게 하지 않고, Monster 클래스 하나와 Breed 클래스 하나만 만듦  

이러면 상속 없이 클래스 두 개만으로 해결할 수 있음  
모든 몬스터를 Monster 클래스의 인스턴스로 표현할 수 있음  
Breed 클래스에는 종족이 같은 몬스터가 공유하는 정보인 최대 체력과 공격 문구가 들어 있음  

몬스터와 종족을 결합하기 위해 모든 Monster 인스턴스는 종족 정보를 담고 있는 Breed 객체를 참조함  
몬스터가 공격 문구를 얻을 때는 종족 객체 메서드를 호출함  
Breed 클래스는 본질적으로 몬스터 '타입'을 정의함  
각각의 종족 **객체**는 개념적으로 다른 **타입**을 의미  

타입 객체 패턴은 코드 수정 없이 새로운 **타입**을 정의할 수 있다는 게 장점  
코드에서 클래스 상속으로 만들던 타입 시스템의 일부를 런타임에 정의할 수 있는 데이터로 옮김 셈  

새로 Breed 인스턴스를 만들어 다른 값을 입력하기만 해도 또 다른 종족을 계속해서 만들 수 있음  
설정 파일에서 읽은 데이터로 종족 객체를 생성하게 만들고 나면, 데이터만으로 전혀 다른 몬스터를 정의할 수 있음  

#### 13.3 패턴
**타입 객체** 클래스와 **타입 사용 객체** 클래스를 정의  
모든 타입 객체 인스턴스는 논리적으로 다른 타입을 의미  
타입 사용 객체는 **자신의 타입을 나타내는 타입 객체를 참조**함  

인스턴스별로 **다른 데이터**는 타입 사용 **객체 인스턴스에 저장**,  
개념적으로 **같은 타입끼리 공유하는 데이터**나 동작은 **타입 객체에 저장**  
같은 타입 객체를 참조하는 타입 사용 객체는 같은 타입인 것처럼 동작  
이러면 상속 처리를 하드코딩하지 않고서도 마치 상속받는 것처럼 비슷한 객체끼리 데이터나 동작 공유 가능  

#### 13.4 언제 쓸 것인가?
타입 객체 패턴은 다양한 '종류'를 정의해야 하는데 개발 언어의 타입 시스템이 유연하지 않아서 코드로 표현하기 어려울 때 적합, 특히 다음 중 하나라도 해당하면 그렇다.
- 나중에 어떤 타입이 필요할지 알 수 없음(새로운 몬스터가 등장하는 DLC를 제공해야 할지도 모름)  
- 컴파일이나 코드 변경 없이 새로운 타입을 추가하거나 타입을 변경하고 싶음  

#### 13.5 주의사항
타입 객체 패턴의 핵심은, 표현력은 좋지만 뻑뻑한 코드 대신 표현력은 좀 떨어져도 훨씬 유연한 데이터로 '타입'을 표현하는 데 있음  
유연성을 얻는 건 좋지만, 타입을 코드가 아닌 데이터로 표현하면서 잃는 것도 있음  

#### 타입 객체를 직접 관리해야 함
C++같은 타입 시스템은 컴파일러가 클래스를 위한 일을 알아서 해준다는 장점이 있음  
각각의 클래스를 정의하는 데이터는 컴파일 될 때 자동으로 실행 파일의 정적 메모리 영역에 들어가 동작함  

타입 객체 패턴에서는 몬스터 인스턴스뿐만 아니라 타입 객체도 직접 관리해야 함  
타입 객체를 생성하고, 이를 필요로 하는 몬스터가 있는 한 메모리에 유지해야 함  
몬스터 인스턴스를 생성할 때 알맞은 종족 객체 레퍼런스로 초기화하는 것도 우리의 일  

컴파일러의 한계를 일부 뛰어넘는 대가로 컴파일러가 해주던 일을 직접 구현해야 함  

#### 타입별로 동작을 표현하기가 더 어려움
상속 방식에서는 메서드를 오버라이드해서 코드로 값을 계산하거나 다른 코드를 호출하는 등 마음대로 할 수 있음  

타입 객체 패턴에서는 Monster 클래스를 상속받아 메서드 오버라이드로 공격 문구를 표현하는게 아니라 종족 객체 변수에 공격 문구를 저장하는 식으로 표현함  

타입 객체로 타입 종속적인 데이터를 정의하기는 쉽지만 타입 종속적인 동작을 정의하기는 어려움  
종족이 다른 몬스터에게는 다른 AI 알고리즘을 적용하고 싶다면 타입 객체로는 구현하기가 더 어려움  

이런 한계를 우회할 방법이 몇 가지 있음  
가장 간단한 방법은 미리 동작 코드를 여러 개 정의해놓은 뒤에 타입 객체 데이터에서 이 중 하나를 선택하는 것  

예를 들어, 몬스터 AI 상태가 '가만히 서 있기', '영웅을 쫓아가기', '무서워서 벌벌 떨기' 중에서 하나라고 해보면 먼저 이들 동작을 각각 구현한 함수를 정의함  
타입 객체가 적당한 함수 포인터를 저장하게 하면 타입 객체를 AI 알고리즘과 연계할 수 있음  

더 나아가 데이터만으로 동작을 정의할 수도 있음  
바이트코드 패턴과 인터프리터 패턴을 이용하면 동작을 표현하는 객체를 만들 수 있음  
파일에서 데이터를 읽어 이들 패턴으로 자료구조를 만들면 동작 정의를 코드에서 데이터로 완전히 옮길 수 있음

#### 13.6 예제 코드
앞에서 본 시스템의 기본을 구현  
Breed 클래스
```
class Breed
{
public:
  Breed(int health, const char* attack)
  : health_(health),
    attack_(attack)
  {}

  int getHealth() { return health_; }
  const char* getAttack() { return attack_; }

private:
  int health_; // Starting health.
  const char* attack_;
};
```

Breed 클래스에는 최대 체력(health_)와 공격 문구(attack_) 필드 두 개만 있음  
Monster 클래스에서 Breed 클래스를 어떻게 쓰는지 보자  
```
class Monster
{
public:
  Monster(Breed& breed)
  : health_(breed.getHealth()),
    breed_(breed)
  {}

  const char* getAttack()
  {
    return breed_.getAttack();
  }

private:
  int    health_; // Current health.
  Breed& breed_;
};
```

Monster 클래스 생성자는 Breed 객체를 레퍼런스로 받음  
이를 통해 상속 없이 몬스터 종족을 정의함  
최대 체력은 생성자에서 breed 인수를 통해 얻음  
공격 문구는 breed_에 포워딩해서 얻음  

여기까지가 타입 객체 패턴의 핵심  
나머지 내용은 덤

#### 생성자 함수를 통해 타입 객체를 좀 더 타입같이 만들기
이제까지는 몬스터를 만들고 그 몬스터에 맞는 종족 객체도 직접 전달함  
이런 방식은 메모리를 먼저 할당한 후에 그 메모리 영역에 클래스를 할당하는 것과 다를 바 없음  

대부분의 OOP 언어에서는 이런 식으로 객체를 만들지 않음  
대신, 클래스의 생성자 함수를 호출해 클래스가 알아서 새로운 인스턴스를 생성하게 함  

```
class Breed
{
public:
  Monster* newMonster() { return new Monster(*this); }

  // 나머지는 동일
};
```

Monster 클래스는 다음과 같이 바뀜
```
class Monster
{
  friend class Breed;

public:
  const char* getAttack() { return breed_.getAttack(); }

private:
  Monster(Breed& breed)
  : health_(breed.getHealth()),
    breed_(breed)
  {}

  int health_; // Current health.
  Breed& breed_;
};
```
가장 큰 차이점은 Breed 클래스의 newMonster 함수  
이게 팩토리 메서드 패턴의 '생성자'

이전 코드에서는 `Monster* monster = new Monster(someBreed);` 으로 몬스터를 생성했다면  
수정하고 나면 `Monster* monster = someBreed.newMonster();` 으로 몬스터를 생성함  

이렇게 하면 객체는 메모리 할당과 초기화 2단게로 생성됨  
Monster 클래스 생성자 함수에서 필요한 모든 초기화 작업을 다 할 수 있음  
예제에서는 breed 객체를 전달하는 게 초기화의 전부지만, 실제 프로젝트에서는 그래픽을 로딩하고, 몬스터 AI를 설정하는 등 다른 초기화 작업이 많이 있을 수 있음  

하지만 이런 초기화 작업은 메모리를 할당한 다음에 진행됨  
아직 제대로 초기화되지 않은 몬스터가 메모리에 먼저 올라가 있는 것  
게임에서는 객체 생성 과정을 제어하고 싶을 때가 종종 있는데, 그럴 때는 보통 커스텀 할당자나 객체 풀 패턴(19장)을 이용해 객체가 메모리 어디에 생성될지를 제어함  

Breed 클래스에 '생성자' 함수를 정의하면 이런 로직을 둘 곳이 생김  
그냥 new를 호출하는게 아니라 newMonster 함수를 호출하면 Monster 클래스에 초기화 제어권을 넘겨주기 전에 메모리 풀이나 커스텀 힙에서 메모리를 가져올 수 있음  
몬스터를 생성할 수 있는 유일한 곳인 Breed 클래스 안에 이런 로직을 둠으로써, 모든 몬스터가 정해놓은 메모리 관리 루틴을 따라 생성되도록 강제할 수 있음

#### 상속으로 데이터 공유하기
여러 개의 비슷한 종족을 수정하거나 만들면 상당히 많은 데이터를 반복해서 고쳐야함  
이럴 땐 종족을 통해 여러 몬스터가 속성을 공유했던 것처럼 여러 종족이 속성 값을 공유할 수 있게 만들면 좋음
상속을 통해 속성 값을 공유할 수 있는데, 이번에는 프로그래밍 언어의 상속 기능이 아닌 타입 객체들끼리 상속할 수 있는 시스템을 직접 구현할 것  

```
class Breed
{
public:
  Breed(Breed* parent, int health, const char* attack)
  : parent_(parent),
    health_(health),
    attack_(attack)
  {}

  int         getHealth();
  const char* getAttack();

private:
  Breed*      parent_;
  int         health_; // Starting health.
  const char* attack_;
};
```
Breed 객체를 만들 땐 상속받을 종족 객체를 넘겨줌  
상위 종족이 없는 최상위 종족은 parent에 NULL을 전달  

하위 객체는 어떤 속성을 상위 객체로부터 받을지, 자기 값으로 오버라이드할지를 제어할 수 있어야함  
예제에서는 최대 체력이 0이 아닐 때, 공격 문구가 NULL이 아닐 때는 자기 값을 쓰고, 아니면 상위 객체 값을 쓰기로함  

두 가지 방식으로 구현할 수 있음  
속성 값을 요청받을 때마다 동적으로 위임하는 방식
```
int Breed::getHealth()
{
  // Override. 오버라이딩
  if (health_ != 0 || parent_ == NULL) return health_;

  // Inherit. 상속
  return parent_->getHealth();
}

const char* Breed::getAttack()
{
  // Override. 오버라이딩
  if (attack_ != NULL || parent_ == NULL) return attack_;

  // Inherit. 상속
  return parent_->getAttack();
}
```

이 방법은 종족이 특정 속성 값을 더 이상 오버라이드하지 않거나 상속받지 않도록 런타임에 바뀔 때 좋음  
메모리를 더 차지하고(상위 객체 포인트 유지를 위해), 속성 값을 반환할때마다 상위 객체들을 줄줄이 확인해보느라 더 느리다는 단점이 있음  

종족 속성 값이 바뀌지 않는다면 생성 시점에 바로 상속을 적용할 수 있음, 이런 걸 **'카피다운'위임** 이라고 함  
객체가 생성될 때 상속받는 속성 값을 하위 타입으로 **복사**해서 넣기 때문  

```
Breed(Breed* parent, int health, const char* attack)
: health_(health),
  attack_(attack)
{
  // Inherit non-overridden attributes.
  if (parent != NULL)
  {
    if (health == 0) health_ = parent->getHealth();
    if (attack == NULL) attack_ = parent->getAttack();
  }
}
```
더 이상 상위 종족 객체를 포인터로 들고 있지 않아도 됨  
생성자에서 상위 속성을 전부 복사했기 때문  
따라서 종족 속성 값을 반환할 때에는 필드 값을 그대로 쓰면 됨
```
int         getHealth() { return health_; }
const char* getAttack() { return attack_; }
```

#### 13.7 디자인 결정
타입 객체 패턴에서는 타입 시스템을 마음대로 만들 수 있어서 설계의 폭이 넓고 여러 가지 재미있는 시도를 해볼 수 있음  
하지만 현실적으로는 가능성이 많이 제한됨  
시스템이 복잡하면 개발 기간이 늘어나고, 유지보수하기가 어려워짐  
게다가 타입 객체 시스템은 프로그래머가 만들더라도, 사용자는 프로그래머가 아닌 경우가 많아서 이해하기 쉽게 만들어야함  
따라서 간단할수록 사용성이 좋음  

- **타입 객체를 숨길 것인가? 노출할 것인가?**  
  앞에서 Monster 클래스는 Breed 객체를 참조하지만 이를 외부에 노출하지 않아 외부 코드에서 몬스터 종족 객체를 직접 접근할 수 없었음  
  몬스터가 종족 객체를 들고 있다는 사실은 상세 구현에 해당하기 때문에 외부 코드 입장에서는 몬스터에 따로 타입이 없어보임  

  물론 Monster 클래스가 Breed 객체를 반환하도록 쉽게 바꿀 수 있음
  ```
  class Monster
  {
  public:
    Breed& getBreed() { return breed_; }

    // Existing code...
  };
  ```

  이러면 Monster 클래스의 설계가 바뀜

  - 타입 객체를 캡슐화하면
    - 타입 객체 패턴의 복잡성이 나머지 다른 코드에는 드러나지 않음  
  
    - 타입 사용 객체는 타입 객체로부터 동작을 선택적으로 오버라이드 할 수 있음  
      몬스터가 거의 죽어갈 때 다른 공격 문구를 보여주고 싶다고 할 때, 다음과 같은 코드를 쉽게 추가할 수 있음  
      ```
      const char* Monster::getAttack()
      {
        if (health_ < LOW_HEALTH)
        {
          return "몬스터가 힘없이 팔을 휘두릅니다.";
        }

        return breed_.getAttack();
      }
      ```
      외부 코드에서 Breed() 객체의 getAttack()을 바로 호출하는 경우는 이런 코드를 추가할 만한 곳이 없음
    - 타입 객체 메서드를 전부 포워딩 해야함  
      굉장히 귀찮은 일, 타입 객체 클래스에 메서드가 많다면 타입 사용 객체 클래스에서 외부에 공개하고 싶은 메서드 전부에 대해 포워딩 메서드를 만들어야 함
  - 타입 객체를 노출하면
    - 타입 사용 클래스 인스턴스를 통하지 않고도 외부에서 타입 객체에 접근할 수 있음  
      타입 사용 객체 없이도 타입 객체인 Breed 메서드를 호출해 새로운 몬스터를 생성할 수 있음

    - 타입 객체가 공개 API의 일부가 됨  
      일반적으로 인터페이스를 적게 노출할수록 복잡성은 줄어들고 유지보수하기는 좋아짐  
      타입 객체를 노출함으로써, 타입 객체가 제공하는 모든 것이 객체 API에 포함됨

- **타입 사용 객체를 어떻게 생성할 것인가?**  
  - 객체를 생성한 뒤에 타입 객체를 넘겨주는 경우  
    - 외부 코드에서 메모리 할당을 제어할 수 있음  
      두 객체 모두 외부에서 생성하기 때문에, 메모리 어디에서 할당할지를 결정할 수 있음  
      덕분에 객체를 여러 다른 메모리(커스텀 메모리 할당자, 스택 등)에 둘 수 있음

  - 타입 객체의 '생성자' 함수를 호출하는 경우  
    - 타입 객체에서 메모리 할당을 제어함  
      아까와는 반대 개념  
      외부에서 타입 객체를 어느 메모리에 생성할지 선택권을 주고 싶지 않다면,  
      타입 객체 팩토리 메서드를 통해서 객체를 만들게 해 메모리를 제어할 수 있음  
      모든 객체를 특정 객체 풀이나 다른 메모리 할당자에서만 생성하도록 제한하고 싶을 때 좋음

- **타입을 바꿀 수 있는가?**  
  객체가 필요하면 타입을 변경하게 할 수도 있음  
  예를 들어 어떤 몬스터는 죽은 뒤에 좀비로 되살아나게 하고 싶다고 할 때  
  몬스터가 죽을 때 종족이 좀비인 몬스터를 새로 만들 수도 있지만,  
  기존 몬스터의 종족을 좀비로 바꾸는 방법도 있음  
  - 타입을 바꿀 수 없다면  
    - 코드를 구현하고 이해하기가 더 쉬움  
      개념상 '타입'은 바뀌지 않는다고 다들 생각하는데, 이런 가정을 코드로 못 박음  

    - 디버깅하기 쉬움  
      몬스터의 상태가 이상해지는 버그를 찾아야 할 때 종족은 변하지 않는다고 가정할 수 있다면 디버깅이 쉬워짐
  - 타입을 바꿀 수 있다면  
    - 객체 생성 횟수가 줄어듬  
      위 예제에서 타입을 바꿀 수 없다면 좀비 몬스터를 만들고 기존 몬스터로부터 유지해야 할 속성 값을 가져운 뒤에 기존 몬스터를 삭제해야 하는 낭비가 생김  
      타입을 바꿀 수 있다면 타입 객체 포인터 값만 바꾸면 됨  

    - 가정을 깨지 않도록 주의해야 함  
      타입 사용 객체와 타입 객체는 상당히 강하게 커플링 됨  
      예를 들어 몬스터의 현제 체력이 해당 종족의 최대 체력보다는 더 클 수 없다고 가정할 수 있음  
      종족 타입을 바꿀 수 있다면 기존 객체 상태를 새로운 타입의 요구사항에 맞춰야 함  
      이를 위해 검증 코드가 필요할 수도 있음

- **상속을 어떻게 지원할 것인가?**
  - 상속 없음  
    - 단순함  
      타입 객체끼리 공유해야 할 데이터가 그리 많지 않다면 코드가 단순해짐  

    - 중복 작업을 해야 할 수도 있음  
      
  - 단일 상속  
    - 그나마 단순한 편  
      구현하기도 쉽지만 이애하기 쉽다는 게 더 중요  
      사용자의 기술적 이해도가 낮다면 손댈 곳이 적을수록 좋음  

    - 속성 값을 얻는 데 오래 걸림  
      타입 객체로부터 원하는 데이터를 얻으려면 실제로 값을 정의한 타입을 찾을 때까지 상속 구조를 타고 올라가야함  
      성능이 민감한 코드에서 이런 식의 런타임 낭비는 바람직하지 않음

  - 다중 상속  
    - 거의 모든 데이터 중복을 피할 수 있음  
      
    - 복잡함  
      다중 상속의 장점은 실무보다는 이론에 가까움  
      좀비 용 타입은 좀비와 용을 상속받는 다고 해보면  
      어떤 속성은 좀비로부터 받고, 어떤 속성은 용으로부터 받아야 할까?  
      다중 상속 시스템을 사용하려면 사용자가 상속 그래프의 흐름을 이해할 수 있어야 하고, 상속 구조를 잘 설계할 수 있는 선경지명을 가져야함  
      고민해볼 가치는 있겠지만, 게임에서 타입 객체에 다중 상속이 필요한 경우는 굉장히 드물 것  
      언제나 그렇듯 단순한게 나음

---
---
## Part 5. 디커플링 패턴  
---
### CHAPTER 14. 컴포넌트
---
#### 14.1, 14.2 의도, 동기
한 개체가 여러 분야를 서로 커플링 없이 다룰 수 있게 함  

플랫포머 게임을 만든다고 가정해보자  
먼저 주인공을 대표하는 클래스를 하나 만들어서 주인공이 게임에서 하는 모든 것들을 넣는게 당연해 보임  

주인공을 조정해야 하니 컨트롤러 입력 값을 읽어 행동으로 바꾸어야 함  
지형이나 플랫폼 같은 레벨과도 상호작용할 수 있도록 물리 및 충돌 처리도 필요함  
주인공이 화면에 나와야 하니 애니메이션과 렌더링도 넣어야함  
물론 소리도 들려야함  

이런식으로 엉망진창이되면 안됨  
분야가 서로 다른 코드는 서로 격리하는 것이 좋음  

AI, 물리, 렌더링, 사운드처럼 분야가 다른 코드끼리는 최대한 서로 모르는게 좋음  
이런 코드를 한 클래스 안에 전부 넣는다면 최악의 소스가 될것임  
클래스가 크다는 것은 정말 사소한 걸 바꾸려고 해도 엄청난 작업이 필요할 수 있음을 의미  
이런 클래스는 기능보다 버그가 더 빨리 늘어나게 됨

#### 고르디우스의 매듭
코드 길이보다 더 큰 문제가 커플링  
여러 게임 시스템이 주인공 클래스 안에서 실타래처럼 얽혀 있음
```
if (collidingWithFloor() && (getRenderState() != INVISIBLE))
{
  playSound(HIT_FLOOR);
}
```

이 코드를 문제없이 고치려면 물리(collidingWithFloor), 그래픽(getRenderState), 사운드(playSound)를 전부 알아야 함  

커플링과 코드 길이 문제는 서로 악영향을 미침  
한 클래스가 너무 많은 분야를 건드리다보니 모든 프로그래머가 그 클래스를 작업해야 하는데, 클래스가 너무 크다 보니 작업하기가 굉장히 어려움  
이런 상황이 심해지면 프로그래머들이 뒤죽박죽 된 주인공 클래스를 손대기싫어서 다른 곳에 땜빵 코드를 넣기 시작함  

#### 매듭 끊기
이 문제는 한 덩어리였던 주인공 클래스를 분야에 따라 여러 부분으로 나누면 됨  

예를 들어 사용자 입력에 관련된 코드는 InputComponent 클래스로 옮겨둔 뒤에, 주인공 클래스가 InputComponent 인스턴스를 갖게 함
주인공 클래스가 다루는 나머지 분야에도 이런 작업을 반복  
이런 작업을 거치게되면 컴포넌트를 묶는 얇은 껍데기 코드 외에는 주인공 클래스에 남는 게 거의 없게됨  

클래스 코드 크기 문제는 클래스를 여러 작은 클래스로 나누는 것만으로 해결했고, 소득은 이뿐만이 아님

#### 열린 구조
컴포넌트 클래스들은 디커플링 되어있음  
PhysicsComponent와 GraphicsComponent는 주인공 클래스 안에 들어 있지만 서로에 대해 알지 못함  
즉, 물리 프로그래머는 화면 처리는 신경쓰지 않고 자기 코드를 수정할 수 있고, 반대도 마찬가지임  

현실적으로는 컴포넌트끼리 상호작용이 필요할 **수도** 있음  
예를 들어 AI 컴포넌트는 주인공이 가려는 곳을 물리 컴포넌트를 통해 알아내야 할 수도 있음  
다만 모든 코드를 한 곳에 섞어놓지 않았기 때문에 서로 **통신이 필요한** 컴포넌트만으로 결합을 재현할 수 있음  

#### 다시 합치기
컴포넌트 패턴의 다른 특징은 이렇게 만든 컴포넌트를 재사용할 수 있다는 점  
주인공 외에도 게임에 필요한 다른 객체들을 생각해보자면  

데커레이션은 덤불이나 먼지같이 볼 수는 있으나 상호작용은 할 수 없는 객체  
프랍은 상자, 바위, 나무같이 볼 수 있으면서 상호작용도 할 수 있는 객체  
존은 데커레이션과는 반대로 보이지는 않지만 상호작용은 할 수 있는 객체  

예를 들어 주인공이 특정 영역에 들어올 때 컷신을 틀고 싶다면 존은 써먹을 수 있음  

컴포넌트를 쓰지 않는다면 이들 클래스를 어떻게 상속해야 할까?  
![image](https://user-images.githubusercontent.com/32252062/74633573-3d698280-51a5-11ea-86bd-e42ef52759f5.png)
(그림 14-1) 단일 상속으로는 두 갈래를 동시에 재사용할 수 없음  

GameObject 클래스는 위치나 방향 같은 기본 데이터를 둠  
Zone은 GameObject을 상속받은 뒤에 충돌 검사를 추가함  
Decoration도 GameObject를 상속받은 뒤 렌더링 기능을 추가함  
Prop은 충돌 검사를 재사용하기 위해 Zone을 상속받음  
하지만 Prop이 렌더링 코드를 재사용하기 위해 Decoration 클래스를 상송하려는 순간 '**죽음의 다이아몬드**' 라고 불리는 다중 상속 문제를 피할 수 없음

뒤집어서 Prop이 Decoration을 상속받게 만들어봐야 충돌 처리 코드 중복은 피할 수 없음  
어떻게 해도 다중 상속 없이는 충돌 처리 코드와 렌더링 코드를 깔끔하게 재사용할 수는 없음  
아니면 모든 기능을 GameObject 클래스에 올려놔야 하는데, 그러면 Zone에는 필요 없는 렌더링 데이터가 들어가고 Decoration에는 쓰지 않는 물리 기능이 들어가게 됨

이것을 컴포넌트로 만들면 상속은 전혀 필요없음  
GameObject 클래스 하나와 PhysicsComponent, GraphicsComponent 클래스 두 개만 있으면 됨  
데커레이션은 GraphicsComponent는 있고, PhysicsComponent는 없는 GameObject이고,  
반대로 존에는 PhysicsComponent가 있고, GraphicsComponent는는 없음  
프랍에는 둘 다 있음  
여기에는 코드 중복도, 다중 상속도 없고, 클래스 개수도 네 개에서 세 개로 줄음  

컴포넌트는 기본적으로 객체를 위한 플러그 앤 플레이라고 볼 수 있음  
개체 소켓에 재사용 가능한 여러 컴포넌트 객체를 꽂아 넣음으로써 복잡하면서 기능이 풍부한 개체를 만들 수 있음

#### 14.3 패턴
**여러 분야를 다루는 하나의 개체**가 있음  
분야별로 격리하기 위해, 각각의 코드를 별도의 **컴포넌트 클래스**에 둠  
이제 개체 클래스는 단순히 이들 **컴포넌트들의 컨테이너** 역할만 함

#### 14.4 언제 쓸 것인가?
컴포넌트는 게임 개체를 정의하는 핵심 클래스에서 가장 많이 사용되지만, 다음 조건 중 하나라도 만족한다면 다른 분야에서도 유용하게 쓸 수 있음  
- 한 클래스에서 여러 분야를 건드리고 있어서, 이들을 서로 디커플링하고 싶음  

- 클래스가 거대해져서 작업하기 어려움

- 여러 다른 기능을 공유하는 다양한 객체를 정의하고 싶음   
단, 상속으로 딱 원하는 부분만 골라서 재사용할 수가 없음

#### 14.5 주의사항
컴포넌트 패턴을 적용하면 클래스 하나에 코드를 모아놨을 때보다 더 복잡해질 가능성이 높음  
한 무리의 객체를 생성하고 초기화하고 알맞게 묶어줘야 하나의 개념적인 '객체'를 만들 수 있기 때문
컴포넌트끼리 통신하기도 더 어렵고, 컴포넌트들을 메모리 어디에 둘지 제어하는 것도 더 복잡함  

코드베이스 규모가 크면 이런 복잡성에서 오는 손해보다 디커플링과 컴포넌트를 통한 코드 재사용에서 얻는 이득이 더 클 수 있음  
하지만 컴포넌트 패턴을 적용하기 전에 아직 있지도 않은 문제에 대한 '해결책'을 오버엔지니어링하는건 아닌지 주의해야 함  

컴포넌트 패턴의 또 다른 문제는 무엇이든지 하려면 한 단계를 거쳐야 할 떄가 많다는 점  
무슨 일이든 컨테이너 객체에서 원하는 컴포넌트부터 얻어야 할 수 있음  
성능이 민감한 내부 루프 코드에서 이런 식으로 포인터를 따라가다 보면 성능이 떨어질 수 있음

#### 14.6 예제 코드
#### 통짜 클래스
컴포넌트 패턴을 어떻게 적용할지를 더 명확하게 알 수 있도록, 먼저 컴포넌트 패턴을 아직 적용하지 않아 모든 기능이 통짜 클래스에 다 들어 있는 주인공(Bjorn)클래스
```
class Bjorn
{
public:
  Bjorn()
  : velocity_(0),
    x_(0), y_(0)
  {}

  void update(World& world, Graphics& graphics);

private:
  static const int WALK_ACCELERATION = 1;

  int velocity_;
  int x_, y_;

  Volume volume_;

  Sprite spriteStand_;
  Sprite spriteWalkLeft_;
  Sprite spriteWalkRight_;
};
```

Bjorn 클래스의 update 메서드는 매 프레임마도 호출됨
```
void Bjorn::update(World& world, Graphics& graphics)
{
  // 입력에 따라 주인공의 속도를 조절
  switch (Controller::getJoystickDirection())
  {
    case DIR_LEFT:
    velocity_ -= WALK_ACCELERATION;
    break;

    case DIR_RIGHT:
    velocity_ += WALK_ACCELERATION;
    break;
  }

  // 속도에 따라 위치를 바꿈
  x_ += velocity_;
  world.resolveCollision(volume_, x_, y_, velocity_);

  // 알맞은 스프라이트를 그림
  Sprite* sprite = &spriteStand_;
  if (velocity_ < 0)
  {
    sprite = &spriteWalkLeft_;
  }
  else if (velocity_ > 0)
  {
    sprite = &spriteWalkRight_;
  }

  graphics.draw(*sprite, x_, y_);
}
```

이 코드는 조이스틱 입력에 따라 주인공을 가속함  
다음으로 물리 엔진을 통해 주인공의 다음 위치를 구한 뒤,  
마지막으로 화면에 주인공인 비외른을 그림  

구현은 굉장히 간단해서 중력도 애니메이션도, 여러 상세한 구현도 다 빠져있음  
그럼에도 코드를 보면 update 함수 하나를 여러 분야의 프로그래머가 작업해야 하고 코드가 더러워지기 시작했다는 것을 알 수 있음  
이런 코드가 몇천 줄이 넘어가면 작업하기 힘들어질 것

#### 분야별로 나누기
먼저 분야 하나를 정해서 관련 코드를 Bjorn에서 별도의 컴포넌트 클래스로 옮김  
가장 먼저 처리되는 입력 분야부터 시작  
Bjorn클래스가 처음 하는 일은 사용자 입력에 따라 주인공의 속도를 조절하는 처리  
그에 해당하는 로직을 별개의 클래스로 옮김
```
class InputComponent
{
public:
  void update(Bjorn& bjorn)
  {
    switch (Controller::getJoystickDirection())
    {
      case DIR_LEFT:
        bjorn.velocity -= WALK_ACCELERATION;
        break;

      case DIR_RIGHT:
        bjorn.velocity += WALK_ACCELERATION;
        break;
    }
  }

private:
  static const int WALK_ACCELERATION = 1;
};
```

어렵게 할 거 없이 Bjorn 클래스의 update 메서드에서 앞부분을 InputComponent 클래스로 옮김  
Bjorn 클래스는 다음과 같이 바뀜
```
class Bjorn
{
public:
  int velocity;
  int x, y;

  void update(World& world, Graphics& graphics)
  {
    input_.update(*this);

    // 속도에 따라 위치를 바꿈
    x += velocity;
    world.resolveCollision(volume_, x, y, velocity);

    // 알맞은 스프라이트를 그림
    Sprite* sprite = &spriteStand_;
    if (velocity < 0)
    {
      sprite = &spriteWalkLeft_;
    }
    else if (velocity > 0)
    {
      sprite = &spriteWalkRight_;
    }

    graphics.draw(*sprite, x, y);
  }

private:
  InputComponent input_;

  Volume volume_;

  Sprite spriteStand_;
  Sprite spriteWalkLeft_;
  Sprite spriteWalkRight_;
};
```

Bjorn 클래스에 InputComponent 객체가 추가됨  
이전에는 사용자 입력을 update()에서 처리했지만, 지금은 입력 컴포넌트에 위임함  
`input_.update(*this);`  

#### 나머지도 나누기
이제 남아 있는 물리 코드와 그래픽스 코드도 같은 식으로 복사 & 붙여넣기를 함
```
class PhysicsComponent
{
public:
  void update(Bjorn& bjorn, World& world)
  {
    bjorn.x += bjorn.velocity;
    world.resolveCollision(volume_,
        bjorn.x, bjorn.y, bjorn.velocity);
  }

private:
  Volume volume_;
};
```
물리 코드를 옮기고 보니 물리 데이터도 같이 옮겨짐  
이제 Volume 객체는 Bjorn이 아닌 PhysicsComponent에서 관리함

마지막으로 렌더링 코드를 옮김
```
class GraphicsComponent
{
public:
  void update(Bjorn& bjorn, Graphics& graphics)
  {
    Sprite* sprite = &spriteStand_;
    if (bjorn.velocity < 0)
    {
      sprite = &spriteWalkLeft_;
    }
    else if (bjorn.velocity > 0)
    {
      sprite = &spriteWalkRight_;
    }

    graphics.draw(*sprite, bjorn.x, bjorn.y);
  }

private:
  Sprite spriteStand_;
  Sprite spriteWalkLeft_;
  Sprite spriteWalkRight_;
};
```

Bjorn 클래스에서 거의 모든 코드를 뽑아냄  
```
class Bjorn
{
public:
  int velocity;
  int x, y;

  void update(World& world, Graphics& graphics)
  {
    input_.update(*this);
    physics_.update(*this, world);
    graphics_.update(*this, graphics);
  }

private:
  InputComponent input_;
  PhysicsComponent physics_;
  GraphicsComponent graphics_;
};
```

이렇게 바뀐 Bjorn 클래스는 두 가지 역할을 함  
먼저 자신을 정의하는 컴포넌트 집합을 관리하고, 컴포넌트들이 공유하는 상태를 들고 있는 역할  
위치 (x, y)와 속도 (velocity) 값을 Bjorn 클래스에 남겨놓은 이유는 두 가지는 다음과 같음  
먼저, 이들 상태는 '전 분야'에서 사용됨  
컴포넌트로 옮기고 싶어도 거의 모든 컴포넌트에서 이 값을 사용하다 보니 어느 컴포넌트에 둘지 애매함  

그보다 더 중요한 이유는 이렇게 하면 컴포넌트들이 서로 커플링되지 않고도 쉽게 통신할 수 있기 때문  
이를 어떻게 활용할 수 있는지 보자

#### 오토-비외른  
동작 코드를 별도의 컴포넌트 클래스로 옮겼지만 아직 **추상화**하지 않음  
Bjorn 클래스는 자신의 동작을 어떤 구체 클래스에서 정의하는지를 정확하게 알고 있음  
이걸 바꿔보자  

사용자 입력 처리 컴포넌트를 인터페이스 뒤로 숨기려고 함  
InputComponent을 다음과 같이 추상 상위 클래스로 바꿔보자  
```
class InputComponent
{
public:
  virtual ~InputComponent() {}
  virtual void update(Bjorn& bjorn) = 0;
};
```

사용자 입력을 처리하던 코드는 InputComponent 인터페이스를 구현하는 클래스로 끌어내림  
```
class PlayerInputComponent : public InputComponent
{
public:
  virtual void update(Bjorn& bjorn)
  {
    switch (Controller::getJoystickDirection())
    {
      case DIR_LEFT:
        bjorn.velocity -= WALK_ACCELERATION;
        break;

      case DIR_RIGHT:
        bjorn.velocity += WALK_ACCELERATION;
        break;
    }
  }

private:
  static const int WALK_ACCELERATION = 1;
};
```

Bjorn 클래스는 InputComponent 구체 클래스의 인스턴스가 아닌 인터페이스의 포인터를 들고 있게 바꿔줌  
```
class Bjorn
{
public:
  int velocity;
  int x, y;

  Bjorn(InputComponent* input)
  : input_(input)
  {}

  void update(World& world, Graphics& graphics)
  {
    input_->update(*this);
    physics_.update(*this, world);                                                                                                                                                                                     
    graphics_.update(*this, graphics);
  }

private:
  InputComponent* input_;
  PhysicsComponent physics_;
  GraphicsComponent graphics_;
};
```

이제는 Bjron 객체를 생성할 때, Bjorn이 사용할 입력 컴포넌트를 다음과 같이 전달할 수 있음  
`Bjorn* bjron = new Bjron(new PlayerInputComponent());`

이제 Bjorn 클래스는 컴포넌트 묶음일 뿐 딱히 '비외른'에 관련된 코드가 남아 있지 않음  

그보다는 게임에서 **모든** 객체가 기본으로 사용하는 게임 객체(GameObject) 클래스로 바꾸는게 더 좋을거 같음  
나머지 물리와 그래픽스 컴포넌트도 입력에서 그랬던 것처럼 인터페이스와 구현부를 나누어보자

```
class PhysicsComponent
{
public:
  virtual ~PhysicsComponent() {}
  virtual void update(GameObject& obj, World& world) = 0;
};

class GraphicsComponent
{
public:
  virtual ~GraphicsComponent() {}
  virtual void update(GameObject& obj, Graphics& graphics) = 0;
};
```

Bjron 클래스는 이름을 범용적인 GameObject로 바꾸고 내붖덕으로 다음과 같은 인터페이스들을 사용하게 됨
```
class GameObject
{
public:
  int velocity;
  int x, y;

  GameObject(InputComponent* input,
             PhysicsComponent* physics,
             GraphicsComponent* graphics)
  : input_(input),
    physics_(physics),
    graphics_(graphics)
  {}

  void update(World& world, Graphics& graphics)
  {
    input_->update(*this);
    physics_->update(*this, world);
    graphics_->update(*this, graphics);
  }

private:
  InputComponent* input_;
  PhysicsComponent* physics_;
  GraphicsComponent* graphics_;
};
```

기존 구체 클래스 역시 이름을 바꾸고 인터페이스를 구현하도록 함  
```
class BjornPhysicsComponent : public PhysicsComponent
{
public:
  virtual void update(GameObject& obj, World& world)
  {
    // Physics code...
  }
};

class BjornGraphicsComponent : public GraphicsComponent
{
public:
  virtual void update(GameObject& obj, Graphics& graphics)
  {
    // Graphics code...
  }
};
```

비외른만을 위한 별도 클래스 없이도 비외른의 원래 기능을 그대로 유지하는 객체를 다음과 같이 만들 수 있음
```
GameObject* createBjorn()
{
  return new GameObject(new PlayerInputComponent(),
                        new BjornPhysicsComponent(),
                        new BjornGraphicsComponent());
}
```

다른 컴포넌트를 조합한 GameObject를 생성하는 함수를 정의하면, 게임에 필요한 온갖 객체를 만들 수 있음

#### 14.7 디자인 결정
컴포넌트 패턴에서 가장 중요한 질문은 '어떤 컴포넌트 집합이 필요한가?'임  
대답은 만들고 있는 게임 장르와 필요에 따라 다름  
게임 코드가 크고 복잡할수록 컴포넌트를 더 세분화 해야 함  

#### 객체는 컴포넌트를 어떻게 얻는가?
- 객체가 필요한 컴포넌트를 알아서 생성할 때  
  - 객체는 항상 필요한 컴포넌트를 가지게 됨  
    컨테이너 객체에서 모든 걸 다 처리하기 때문에 실수로 잘못된 컴포넌트를 객체에 연결하거나 컴포넌트 연결을 잊어버려서 게임이 깨질 일이 없음

  - 객체를 변경하기가 어려움  
    컴포넌트 패턴의 강점 중 하나는 컴포넌트 재조합만으로 새로운 종류의 객체를 만들 수 있다는 점  
    어떤 컴포넌트를 사용할지를 하드코딩해놓으면 이런 유연성을 잃게 됨

- 외부 코드에서 컴포넌트를 제공할 때  
  - 객체가 훨씬 유연해짐  
    컴포넌트만 바꿔도 전혀 다르게 동작하는 객체를 만들 수 있음  
    극단적으로 유연하게 만든다면, 객체는 일반적인 컴포넌트 컨테이너가 되어 다른 용도로도 계속해서 재사용할 수 있음

  - 객체를 구체 컴포넌트 자료형으로부터 디커플링할 수 있음  
    밖에서 컴포넌트를 전달할 수 있다면, 인터페이스를 상속받은 컴포넌트 객체를 전달할 가능성이 높음  
    객체는 컴포넌트의 인터페이스만 알지, 이게 어떤 구체 클래스인지는 모르기 때문에 구조를 캡슐화하기 더 좋음

#### 컴포넌트들끼리는 어떻게 통신할 것인가?
컴포넌트들이 서로 완전히 격리된 채로 동작하는 게 이상적이겠지만, 실제로 그렇게 만들기는 어렵기 때문에 서로 간의 통신이 필요  

- 컨테이터 객체의 상태를 변경하는 방식  
  - 컴포넌트들은 서로 디커플링 상태를 유지  
    InputComponent에서 비외른의 속도를 변경하고, PhysicsComponent에서는 그 값을 사용하면  
    이들 두 컴포넌트는 서로를 몰라도 됨  
    그저 비외른의 속도가 알 수 없는 무엇인가에 의해 변경되었다고 짐작만 할 뿐  

  - 컴포넌트들이 공유하는 정보를 컨테이너 객체에 전부 넣어야 함  
    어떤 상태는 일부 컴포넌트에서만 사용하기도 함  
    예를 들어 애니메이션 컴포넌트와 렌더링 컴포넌트는 객체의 그래픽 관련 상태를 공유해야 하는데, 이런 상태를 다른 모든 컴포넌트가 접근할 수 있는 컨테이너 객체로 올려놓는다면 객체 클래스가 지저분해질 수 있음  
    심지어 컴포넌트 조합에 따라 컨테이너 객체의 상태를 전혀 사용하지 않을 수도 있음  
    보이지 않은 객체에는 컴포넌트 객체에 들어 있는 렌더링 관련 데이터가 메모리 낭비일 뿐  

  - 컴포넌트끼리 암시적으로 통신하다 보니 컴포넌트 실행 순서에 의존하게 됨  
    코드를 여러 컴포넌트로 나눈 뒤에도 실행 순서는 바뀌지 않도록 주의해야 함  
    그렇지 않으면 미묘하게 찾기 어려운 버그가 생길 수 있음  
    그래픽스 컴포넌트를 먼저 업데이트하면 비외른을 이번 프레임이 아닌 이전 프레임 위치에 잘못 그리게 됨  
    컴포넌트 개스와 코드가 많을수록 이런 버그를 피하기 어려움

- 컴포넌트가 서로 참조하는 방식  
  서로 통신해야 하는 컴포넌트들이 컨테이너 객체를 통하지 않고 직접 참조하게 만드는 방식  

  비외른이 점프를 할 수 있게 만든다고 할 때  
  그래픽스 코드는 점프 스프라이트를 그려야 할지 여부를 판단  
  이를 위해 주인공이 땅에 서 있는지를 물리 엔진에 물어봐야 함  
  그래픽스 컴포넌트가 물리 컴포넌트를 직접 알고 있다면 이를 쉽게 해결 가능
  ```
  class BjornGraphicsComponent
  {
  public:
    BjornGraphicsComponent(BjornPhysicsComponent* physics)
    : physics_(physics)
    {}

    void Update(GameObject& obj, Graphics& graphics)
    {
      Sprite* sprite;
      if (!physics_->isOnGround())
      {
        sprite = &spriteJump_;
      }
      else
      {
        // 나머지 그래픽스 코드
      }

      graphics.draw(*sprite, obj.x, obj.y);
    }

  private:
    BjornPhysicsComponent* physics_;

    Sprite spriteStand_;
    Sprite spriteWalkLeft_;
    Sprite spriteWalkRight_;
    Sprite spriteJump_;
  };
  ```
  비외른의 GraphicsComponent를 생성할 때 이에 맞는 PhysicsComponent를 생성자 인수에 레퍼런스로 제공  
  - 간단하고 빠름  
    한 객체가 다른 객체 메서드를 직접 호출해 통신  
    컴포넌트는 자기가 참조하는 컴포넌트에서 제공하는 어떤 메서드라도 아무런 제한 없이 호출할 수 있음  

  - 두 컴포넌트가 강하게 결합됨  
    아무런 제한이 없다 보니 생기는 단점  
    서로 통신하는 컴포넌트끼리만 커플링이 생기기 때문에 통짜 클래스 형태일 때보다는 나음

- 메시지를 전달하는 방식  
  가장 복잡한 대안  
  컨테이너 객체에 간단한 메시징 시스템을 만든 뒤에, 각 컴포넌트들이 서로에게 정보를 뿌리게 할 수 있음  

  먼저 모든 컴포넌트가 상속받아야 하는 기본 인터페이스인 Component를 정의
  ```
  class Component
  {
  public:
    virtual ~Component() {}
    virtual void receive(int message) = 0;
  };
  ```

  컨테이너 객체에는 메시지를 보내는 메서드를 추가  
  ```
  class ContainerObject
  {
  public:
    void send(int message)
    {
      for (int i = 0; i < MAX_COMPONENTS; i++)
      {
        if (components_[i] != NULL)
        {
          components_[i]->receive(message);
        }
      }
    }

  private:
    static const int MAX_COMPONENTS = 10;
    Component* components_[MAX_COMPONENTS];
  };
  ```

  컴포넌트가 컨테이너에 메시지를 보내면, 컨테이너는 자기에게 있는 모든 컴포넌트에 이를 전파  
  (여기에는 처음 메시지를 보낸 컴포넌트도 포함, 피드백 루프에 빠지지 않도록 주의)

  - 하위 컴포넌트들은 디커플링됨  
    컴포넌트들은 메시지 값과 커플링될 뿐 컴포넌트끼리는 디커플링 상태를 유지

  - 컨테이너 객체는 단순  
    메시지만 무작정 전달하면 되기 때문에 컨테이너 객체는 단순  
    특정 분야에 한정된 정보를 컨테이너 객체에 노출하지 않고도 컴포넌트끼리 주고받을 수 있음

세 가지 통신 방식 중에서 정답은 따로 없음  
상태 공유 방식은 위치나 크기 같이 모든 객체에서 당연히 있을 거라고 생각하는 기본적인 정보를 공유하기 좋음  

어떤 분야들은 서로 별개이나 꽤나 가깝게 연관될 수 있음  
애니메이션과 렌더링, 사용자 입력과 AI, 물리와 충돌 같은 것들이 그럼  
이런 쌍이 서로 다른 컴포넌트로 존재한다면 각자 자신의 짝궁 컴포넌트를 직접 알게 하는 게 작업하기에 가장 편할 수 있음  

메시징은 호출하고 나서 신경 안 써도 되는 '사소한' 통신에 쓰기 좋음  
예를 들어 물리 컴포넌트에서 객체가 무엇인가 충돌했다고 전파하면 오디오 컴포넌트가 이를 받아서 소리를 내는 식

---
---
### CHAPTER 15. 이벤트 큐
---
#### 15.1, 15.2 의도, 동기
메시지나 이벤트를 보내는 시점과 처리하는 시점을 디커플링  

#### GUI 이벤트 루프  
버튼을 클릭하거나 메뉴를 선택하거나 키보드를 눌러서 프로그램과 상호작용할 때마다, 운영체제는 이벤트를 만들어 프로그램 쪽으로 전달함  
프로그램에서는 이를 받아서 원하는 행위를 처리하도록 이벤트 핸들러 코드를 전달해야 함  
이벤트를 받기 위해서는 **이벤트 루프**가 있어야함  

```
while (running)
{
  Event event = getNextEvent();
  // Handle event...
}
```

getNextEvent()는 아직 처리하지 않은 사용자 입력을 가져옴  
이를 이벤트 핸들러로 보내면 애플리케이션이 움직임  
재미있는 점은 애플리케이션이 **자기가 원할 때** 이벤트를 **가져온다**는 점  

이벤트를 원할 때 가져올 수 있다는 얘기는 OS가 디바이스 드라이버로부터 입력 값을 받은 뒤 애플리케이션에서 getNextEvent()로 가져갈 때까지 그 값을 어디엔가 저장해 둔다는 뜻  

그 '어딘가'가 바로 **큐**  

사용자 입력이 들어오면, OS는 아직 처리 안 된 이벤트 큐에 추락  
getNextEvent()는 가장 먼저 들어온 이벤트부터 큐에서 꺼내 애플리케이션에 전달  
![image](https://user-images.githubusercontent.com/32252062/74910402-c67af680-53fd-11ea-83a5-58e298f9d499.png)
(그림 15-1) 이벤트는 큐를 통해 OS로부터 애플리케이션으로 전달됨  

#### 중앙 이벤트 버스  
이런 이벤트 주도 방식으로 구현된 게임은 거의 없지만,  
게임에서 자체 이벤트 큐를 만들어 중추 통신 시스템으로 활용하는 경우는 흔함  
게임 시스템들이 디커플링 상태를 유지한 채로 서로 고수준 통신을 하고 싶을 때 이를 사용함  

특정 인-게임 이벤트가 발생할 때 풍선 도움말을 보여주는 튜토리얼 시스템을 만든다고 해볼 때  
예를 들어, 플레이어가 괴물을 처음으로 잡으면 'x를 눌러 전리품을 획득하세요'라는 말풍선을 보여주고 싶다고 하면  

코드에다가 튜토리얼을 보여줄지 검사하는 코드를 여기저기 끼워 넣는 대신에  
중앙 이벤트 큐를 만들면 어느 게임 시스템에서도 큐에 이벤트를 보낼 수 있음  
전투 코드라면 매번 적을 죽일 때마다 '적이 죽었음' 이벤트를 보내는 식  

마찬가지로 모든 게임 시스템은 큐로부터 이벤트를 받을 수 있음  
튜토리얼 코드는 '적이 죽었음' 이벤트를 받으면 알려달라고 큐에 자기 자신을 등록해놓음  
이렇게하면 서로를 모르더라도 전투 시스템으로부터 튜토리얼 코드로 적이 죽었다는 사실을 전달할 수 있음

![image](https://user-images.githubusercontent.com/32252062/74910466-e7dbe280-53fd-11ea-8d71-0b4e8c4f04e0.png)
(그림 15-2) 전투 시스템과 튜토리얼 시스템은 공유 큐를 통해서 서로 상호작용함

이벤트 큐가 언제나 게임 전체 통신 시스템으로만 사용되어야 하는 건 아님  
클래스 하나, 분야 하나에서도 유용하게 사용할 수 있음

#### 사운드 시스템 예제
아이디와 볼륨을 받아 사운드를 출력하는 API를 제공하는 단순한 '오디오 엔진'부터 만들어 보면
```
class Audio
{
public:
  static void playSound(SoundId id, int volume);
};
```

오디오 엔진은 적당한 사운드 리소스를 로딩하고 이를 출력할 수 있는 채널을 찾아서 틀어 줌  
오디오 시스템이 어딘가에 구현되어 있다고 가정  
메서드 구현은 다음과 같음  
```
void Audio::playSound(SoundId id, int volume)
{
  ResourceId resource = loadSound(id);
  int channel = findOpenChannel();
  if (channel == -1) return;
  startSound(resource, channel, volume);
}
```

위 코드를 소스 관리 툴에 체크인하고 사운드 파일을 만들고 나면 코드 여기 저기에서 playSound()를 호출할 수 있음  

예를 들어 UI 코드에서 선택한 메뉴가 바뀔 때 작게 삑삑 소리를 내고 싶다면 다음과 같이 하면 됨  
```
class Menu
{
public:
  void onSelect(int index)
  {
    Audio::playSound(SOUND_BLOOP, VOL_MAX);
    // 그 외...
  }
};
```

이 상태에서 메뉴를 옮겨다니다 보면 화면이 몇 프레임 정도 멈출 때가 있음. 무엇이 문제일까?

#### 문제 1 : API는 오디오 엔진이 요청을 완전히 처리할 때까지 호출자를 블록함  
playSound()는 동기적(synchronous), 스피커로부터 삑 소리가 나기 전까지 API는 블록됨  
사운드 파일을 먼저 디스크에서 로딩하기라도 해야 한다면 더 오래 기다려야 하고, 그 동안 게임은 멈춤  

또 다른 문제는 몬스터가 플레이어에게 피해를 입으면 비명 소리를 내도록 AI 코드를 추가 했다고 했을 때,  
두 마리 이상의 몬스터를 동시에 공격하면, 같은 비명 소리를 동시에 두 개 틀어야 함  
같은 소리 파형 두 개를 동시에 출력하면, 하나의 소리를 두 배 크기로 트는 것과 같아서 거슬리게 됨  
더 많은 몬스터를 동시에 친다면 하드웨어적으로 동시에 출력할 수 있는 소리에는 한계가 있어서 그 이상 되면 사운드 출력이 무시되거나 끊김  

이런 문제를 해겨하려면 전체 사운드 호출을 취합하고 우선순위에 따라 나열해야 함  
하지만 예제의 오디오 API는 playSound()를 하나씩 처리하기 때문에 사운드 요청을 한 번에 하나밖에 볼 수 없음

#### 문제 2 : 요청을 모아서 처리할 수 없음
최근 멀티코어 하드웨어에서 위 코드가 실행된다고 할 때 멀티코어를 최대한 활용하려면 랜더리용 슥레드, AI용 스레드처럼 게임 시스템들을 별로의 스레드로 나눠야 함  
playSound API가 동기식이기 때문에 코드는 호출한 쪽 스레드에서 실행됨  

오디오용 스레드를 별도로 만들면 문제가 더 심해짐  

#### 문제 3 : 요청이 원치 않는 스레드에서 처리됨  
이 모든 문제의 원인은 오디오 엔진이 playSound() 호출을 '하던 일을 멈추고 당장 사운드를 틀어'라고 해석하는데 있음  
**즉시성**이 문제임  
다른 게임 시스템에서는 자기가 편할 때 playSound()를 호출함  
하지만 오디오 엔젠 입장에서는 playSound()를 호출받았을때가 사운드 요청을 처리하기에 항상 적당한 것은 아님  
이를 해결하기 위해 요청을 **받는** 부분과 요청을 **처리하는** 부분을 분리해야함

#### 15.3 패턴
큐는 요청이나 알림을 들어온 순서대로 저장  
알림을 보내는 곳에서는 요청에 큐에 넣은 뒤에 결과를 기다리지 않고 리턴  
요청을 처리하는 곳에서는 큐에 들어 있는 요청을 나중에 처리  
요청은 그곳에서 직접 처리될 수도 있고, 다른 여러 곳으로 보내질 수도 있음  
이를 통해 요청을 보내는 쪽과 받는 쪽을 코드뿐만 아니라 시간 측면에서도 디커플링

#### 15.4 언제 쓸 것인가?
메시지를 보내는 곳과 받는 곳을 분리하고 싶을 뿐이라면 다른 패턴들로 덜 복잡하게 이를 해결 가능  
메시지를 보내는 시점과 받는 시점을 분리하고 싶을 때만 큐가 필요함  

밀어 넣기(pushing)과 가져오기(pulling)라는 면에서 생각해보면,  
'A 코드는 B 코드에 일을 요청한다.'  
A 입장에서는 B에게 요청을 **밀어 넣는** 게 자연스러움  
반대로 B 입장에서는 **자기가** 편할 때 요청을 **가져와서** 처리하는게 편함  

한쪽에서는 밀어 넣고, 다른 쪽에서는 가져온다면 이들 사이에 버퍼가 필요  
이게 다른 디커플링 패턴은 제공 못하고 큐만 제공할 수 있는 기능  

큐는 요청 **받는 쪽**에 제어권을 제공  
받는 쪽은 처리를 지연할 수도 있고, 요청을 모아서 처리하거나 전부 다 버릴 수도 있음  
요청을 보내는 쪽으로부터 제어권을 뺏어왔기 때문에 이런게 가능  

보내는 쪽에서 처리 응답을 받아야 한다면 큐를 쓰는 게 적합하지 않음

#### 15.5 주의사항
이벤트 큐는 복잡하고 게임 구조에 전반적으로 영향을 미치는 경향이 있음  
따라서 이벤트 큐 패턴을 어떻게 사용할지, 혹은 정말 쓸 것인지를 잘 생각해야 함  

- 중앙 이벤트 큐는 전역 변수와 같음  
  중앙 이벤트 큐는 강력한 기반 시스템이지만 강력하다는 게 항상 좋은 것은 아님  

  어떤 상태가 프로그램 어디에서나 접근 가능하면, 상호의존성 문제가 생기기 마련  

  이벤트 큐 패턴에서는 중앙 이벤트 큐를 간단한 프로토콜로 깔끔하게 래핑하지만, 그래도 전역이다 보니 관련 문제가 여전히 존재  

- 월드 상태는 언제든 바뀔 수 있음  
  이벤트를 받았을 때는 현재 월드 상태가 이벤트가 만들어진 당시 상태와는 다를 수 있다는 점을 주의해야 함  
  그렇기 때문에 동기적으로 처리되는 이벤트보다 큐에 들어가는 이벤트에서는 데이터가 훨씬 더 많이 필요함  

  동기 이벤트에서는 '뭔가 발생했다'는 알림을 받은 쪽에서 바로 상황을 확인할 수 있음  
  큐를 사용하게 되면 이런 일시적인 정보를 이벤트와 함께 담아야 나중에 쓸 수 있음

- 피드백 루프에 빠질 수 있음  
  모든 이벤트, 메시지 시스템은 다음과 같은 순환이 생기지 않도록 주의해야 함  
  1. A가 이벤트를 보냄
  2. B가 이벤트를 받아 응답으로 다른 이벤트를 보냄
  3. 이 이벤트가 우연찮게 A에서 처리해줘야 하는 작업이라 A가 이벤트를 받음, 그에 대한 응답으로 다른 이벤트를 보냄
  4. 2번으로 감  

  메시징 시스템이 동기적이라면 스택 오버플로 크래시가 나기 때문에 순환을 금방 찾을 수 있음  
  하지만 비동기인 큐 시스템일 때는 콜스택이 풀려서 A와 B가 쓸데없이 계속 이벤트를 주고받는데도 게임은 계속 실행됨  
  이런 문제를 피하는 일반적인 규칙은 이벤트를 **처리하는** 코드 내에서는 이벤트를 **보내지 않는 것**
  
#### 15.6 예제 코드
앞에서 본 문제들을 해결해보자  

첫 번째 문제는 API가 **블록**된다는 점  
사운드 함수를 실행하면 playSound()에서 리소스를 로딩해 실제로 스피커에서 소리가 나오기 전에는 아무것도 못 하고 기다려야 했음  

playSound()가 바로 리턴하게 만들려면 사운드 출력 작업을 지연시킬 수 있어야 함  
요청을 보류해놨다가 나중에 사운드를 출력할 때 필요한 정보를 **저장**할 수 있도록 간단한 구조체부터 정의  

```
struct PlayMessage
{
  SoundId id;
  int volume;
};
```

Audio 클래스가 보류된 사운드 관련 메시지를 저장해둘 수 있도록 저장 공간을 만들자  
알고리즘을 생각하면 피보나치 힙이나 스킵 리스트, 하다못해 연결 리스트 같은 것을 쓰겠지만  
실무에서 동일한 데이터들을 저장하는 가장 좋은 방법은 대부분 기본 배열을 쓰는 것  

기본 배열의 장점은 다음과 같음
- 동적 할당이 필요 없음
- 메모리에 추가 정보나 포인터를 저장하지 않아도 됨
- 메모리가 이어져 있어서 캐시하기 좋음

```
class Audio
{
public:
  static void init()
  {
    numPending_ = 0;
  }

  // Other stuff...
private:
  static const int MAX_PENDING = 16;

  static PlayMessage pending_[MAX_PENDING];
  static int numPending_;
};
```
배열 크기는 최악의 경우에 맞춰서 조정하면 됨  
소리를 내려면 배열 맨 뒤에 메시지를 넣으면 됨  

```
void Audio::playSound(SoundId id, int volume)
{
  assert(numPending_ < MAX_PENDING);

  pending_[numPending_].id = id;
  pending_[numPending_].volume = volume;
  numPending_++;
}
```

이렇게 하면 playSound()를 바로 리턴시킬 수 있음  
사운드 출력 코드는 update 메서드로 옮겨놓음
```
class Audio
{
public:
  static void update()
  {
    for (int i = 0; i < numPending_; i++)
    {
      ResourceId resource = loadSound(pending_[i].id);
      int channel = findOpenChannel();
      if (channel == -1) return;
      startSound(resource, channel, pending_[i].volume);
    }

    numPending_ = 0;
  }

  // Other stuff...
};
```

이제 Audio::update()를 어딘가 적당한 곳에서 호출하면 됨  
적당한 곳은 상황에 따라 다름, 메인 게임 루프에서 호출해도 되고, 별도의 오디오 스레드에서 호출해도 됨  

이렇게 하면 동작은 하지만, update()를 한 번 호출해서 모든 사운드 요청을 다 처리할 수 있다고 가정하고 있음  
사운드 리소스가 로딩된 다음에 비동기적으로 요청을 처리해야 한다면 이렇게는 안됨  
update()에서 한 번에 하나의 요청만 처리하게 하려면 버퍼에서 요청을 하나씩 꺼낼 수 있어야함  
즉, 진짜 **큐**가 필요함

#### 원형 버퍼
원형 버퍼는 일반 배열의 장점은 다 있으면서도 큐 앞에서부터 순차적으로 데이터를 가져올 수 있음  
배열 앞에서부터 데이터를 꺼내면 나머지 데이터를 앞으로 옮겨줘야하는 점 때문에 속도가 느릴 수 있음  
이를 해결하기위해 연결 리스트를 사용함  

- 머리(head)는 큐에서 요청을 **읽을** 위치, 가장 먼저 보류된 요청을 가리킴
- 꼬리(tail)은 반대, 배열에서 새로운 요청이 **들어갈** 자리를 가리킴  
  꼬리는 큐에 있는 **마지막 요청의 다음칸**을 가리킨다는 점에 주의  

playSound()는 배열 맨 뒤에 요청을 추가  
머리는 0번 인덱스에 있고 꼬리는 오른쪽으로 증가함  

![image](https://user-images.githubusercontent.com/32252062/75343083-0436be00-58db-11ea-9823-725a53799596.png)
(그림 15-3) 배열을 이벤트로 채움  

먼저 원래 있던 인덱스 대신 머리와 꼬리를 멤버 변수로 추가
```
class Audio
{
public:
  static void init()
  {
    head_ = 0;
    tail_ = 0;
  }

  // 메서드...
private:
  static int head_;
  static int tail_;

  // 배열...
};
```

playSound()에서는 numPending_이 tail_로 바뀌었을 뿐 나머지는 그대로
```
void Audio::playSound(SoundId id, int volume)
{
  assert(tail_ < MAX_PENDING);

  // 배열 맨 뒤에 추가
  pending_[tail_].id = id;
  pending_[tail_].volume = volume;
  tail_++;
}
```

```
void Audio::update()
{
  // If there are no pending requests, do nothing.
  if (head_ == tail_) return;

  ResourceId resource = loadSound(pending_[head_].id);
  int channel = findOpenChannel();
  if (channel == -1) return;
  startSound(resource, channel, pending_[head_].volume);

  head_++;
}
```
update에서는 머리가 가리키는 요청을 처리한 후에는 머리 포인터를 오른쪽으로 옮겨서 요청 값을 버림  
머리와 꼬리가 겹쳤는지를 보고 큐가 비었는지를 확인할 수 있음(꼬리가 마지막 데이터 다음 위치이기 때문)  

값을 뒤로 추가하고 앞에서 꺼낼 수 있는 큐가 만들어짐  
물론 큐를 통해서 요청을 처리하는 동안 머리와 꼬리는 계속 오른쪽으로 이동함  
언젠가 tail_이 배열 끝에 도달하면 더 이상 추가할 수 없지만, 여기서부터 원형 버퍼의 장점이 나옴  

![image](https://user-images.githubusercontent.com/32252062/75343290-77d8cb00-58db-11ea-9175-0139471be3ed.png)  
(그림 15-4) 큐는 빈칸을 뒤로 남겨운 채로 배열을 따라 이동

꼬리뿐만 아니라 머리도 오른쪽으로 움직임  
이 말은 더 이상 사용하지 않는 배열 값이 배열 앞에 쌓여 있는 셈  
그러니 꼬리가 배열 끝에 도달하면 다시 배열 앞으로 보내면 됨  
마치 원형 배열같이 작동하기 때문에 이런 큐를 원형 버퍼라고 부름

![image](https://user-images.githubusercontent.com/32252062/75343295-7909f800-58db-11ea-9618-aae249f2982f.png)
(그림 15-5) 꼬리는 배열 맨 앞으로 되돌아감  

구현은 굉장히 쉬움  
데이터를 큐에 넣을 때 꼬리가 배열 끝까지 가면 다시 배열 앞으로 보내면 됨  

```
void Audio::playSound(SoundId id, int volume)
{
  assert((tail_ + 1) % MAX_PENDING != head_);

  // 배열 맨 뒤에 추가
  pending_[tail_].id = id;
  pending_[tail_].volume = volume;
  tail_ = (tail_ + 1) % MAX_PENDING;
}
```

원래 tail_++였던 코드를 tail_에 1을 더한 뒤에 배열 크기로 나눈 나머지 값을 받게 바꿔서, 끝에 도달하면 맨 앞으로 가게 함  

단언문도 추가함, 큐는 오버플로되면 안됨  
큐에 요청이 MAX_PENDING개 미만으로 들어 있다면, 머리와 꼬리 사이에 칸이 남아있게 됨  
큐가 가득 찬 뒤에는 꼬리가 머리와 충돌한 뒤에 값을 덮어쓰기 시작함  
단언문을 통해서 이런 일이 벌어지지 않게 함

update()에서는 머리도 배열을 순회하도록 처리
```
void Audio::update()
{
  // If there are no pending requests, do nothing.
  if (head_ == tail_) return;

  ResourceId resource = loadSound(pending_[head_].id);
  int channel = findOpenChannel();
  if (channel == -1) return;
  startSound(resource, channel, pending_[head_].volume);

  head_ = (head_ + 1) % MAX_PENDING;
}
```
이로써 동적 할당도 필요 없고 데이터를 옮길 필요도 없고 단순 배열만큼 캐시하기 좋은 큐가 완성됨  
(큐의 최대 용량(MAX_PENDING)이 신경 쓰인다면 늘어나는 배열을 사용하면 됨,  
큐가 가득 차면 현재 큐보다 큰 크기로 배열을 만들어 데이터를 옮기는 방식  
배열이 늘어날 때마다 데이터를 복사해야 하는 단점을 있지만, 평균적으로 상수 시간 안에 가능함)

#### 요청 취합하기
첫 번째 문제는 같은 소리를 동시에 틀면 동시에 커지는 현상이었음  
이제는 대기 중인 요청을 확인할 수 있기 때문에 같은 요청이 잇다면 병합해버리면 됨  

```
void Audio::playSound(SoundId id, int volume)
{
  // 보류 중인 요청을 쭉 살펴봄
  for (int i = head_; i != tail_;
       i = (i + 1) % MAX_PENDING)
  {
    if (pending_[i].id == id)
    {
      // 둘 중에 소리가 큰 값으로 덮어씀
      pending_[i].volume = max(volume, pending_[i].volume);

      // 이 요청은 큐에 넣지 않음
      return;
    }
  }

  // Previous code...
}
```

같은 소리를 출력하려는 요청이 먼저 들어와 있다면, 둘 중 소리가 큰 값으로 합쳐짐  
이런 '취합' 과정은 굉장히 기초적이지만, 배치 같은 방식으로 처리할 수 있음  

요청을 **처리**할 때가 아니라, **큐에 넣기 전**에 취합이 일어난다는 점에도 주의  
어차피 취합하면서 없어질 요청은 큐에 둘 필요도 없고, 구현하기도 더 쉬움  

호출하는 쪽의 처리 부담이 늘어난다는 단점은 있음  
playSound()는 전체 큐를 쭉 돈 다음에야 리턴하기 때문에 큐가 크면 느릴 수 있음  
그럴 때는 update()에서, 즉 요청을 처리할 때 취합하는 게 나을 수도 있음  

이벤트 큐 패턴은 요청자가 실제로 요청이 언제 처리되는지를 모르게 막음  
하지만 큐가 이런 식으로 상황에 따라 다르게 반응한다면, 큐에 넣은 요청이 실제로 처리될 때까지 걸리는 시간이 동작에 영향을 미칠 수 있음  
먼저 이래도 괜찮은지를 확인하고 이 방법을 사용해야 함  

#### 멀티 스레드
가장 골치 아픈 문제  
동기식으로 만든 오디오 API에서는 playSound()를 호출한 스레드에서 요청도 같이 처리해야 했음  
그리 바람직한 방법이 아님  

멀티코어 하드웨어에서는 멀티스레드를 사용해 하드웨어 성능을 최대한 끌어내야 함  
스레드에 코드를 분배하는 방법은 다양하지만, 오디오, 렌더링, AI 같이 분야별로 할당하는 전략을 많이 사용함  

우리는 이미 멀티코어를 적용하기 위한 세 가지 주요 조건을 준비해둠  
1. 사운드 요청 코드와 사운드 재생 코드가 분리되어 있음
2. 양쪽 코드 사이에 마샬링(marshalling)을 제공하기 위한 큐가 있음
3. 큐는 나머지 코드로부터 캡슐화되어 있음  

이제 큐를 변경하는 코드인 playSound()와 update()를 스레드 안전하게 만들기만 하면 됨  

큐가 동시에 수정되는 것만 막으면 됨  
playSound()는 몇몇 필드에 값만 할당할 뿐 작업이 많지 않기 때문에 블록을 해도 그리 오래 걸리지 않음  
update()에서는 조건 변수 같은 것으로 기다리게 만들면 처리할 요청이 없는 동안 CPU 낭비를 막을 수 있음

#### 15.7 디자인 결정
많은 게임이 이벤트 큐를 통신 구조의 핵심 요소로 사용함  
메시지를 분산하고 필터링하는 온갖 복잡한 구조를 엄청난 시간을 써가면서 만들 수도 있음  
하지만 복잡한 것부터 무작정 만들지 말고, 간단한 것부터 만들어 보는 것이 좋음  

#### 큐에 무엇을 넣을 것인가?
- 큐에 이벤트를 넣는 경우  
  '이벤트' 혹은 '통지'는 '몬스터가 죽었음'같이 **이미** 발생한 사건을 표현  
  이벤트를 큐에 넣으면, 다른 객체가 비동기 관찰자 패턴(4장) 같은 방식으로 이벤트에 대해 **반응**할 수 있음  
  - 복수 개의 리스너를 지원해야 할 때도 많음  
    큐에는 이미 발생한 일이 들어 있기 때문에, 보내는 쪽에서는 누가 그걸 받는지 신경 쓰지 않음

  - 큐의 범위가 더 넓은 편  
    이벤트 큐는 이벤트를 원하는 누구에게든지 전파하는 용도로 사용됨, 리스너가 최대한 유연할 수 있도록 큐를 더 전역적으로 노출하는 편

- 큐에 메시지를 넣는 경우  
  '메시지' 또는 '요청'은 '사운드 틀기' 같이 **나중에 실행**했으면 하는 행동을 표시  
  서비스에 비동기적으로 API를 호출하는 것과 비슷하다고 보면 됨  

  - 대부분 리스너가 하나임  
    예제에서 큐에 넣은 메시지는 오디오 API가 소리를 틀라고 만든 요청  
    다른 게임 코드 여기저기에서 이 큐에 들어 있는 메시지를 가져가버릴 수 있다면 좋을게 없음  

#### 누가 큐를 읽는가?
- 싱글캐스트 큐  
  큐가 어떤 클래스의 API 일부일 때 적합  
  앞에서 본 오디오 예제에서처럼 호출하는 쪽에서는 playSound 메서드만 보일 뿐  
  - 큐는 밖에서는 보이지 않는 내부 구현이 됨  
    보내는 쪽에서는 메시지를 보냈다는 것만 알음  

  - 큐가 더 캡슐화되어 있음  
    다른 조건이 같다면 캡슐화가 더 되어 있을수록 좋음  

  - 리스너 간에 경쟁을 고민하지 않아도 됨  
    리스너가 여러 개라면 모든 리스너에게 이벤트를 보낼지, 하나의 리스너에 이벤트를 하나씩 나눠줄지 정해야함  
    어느쪽이든 리스너들은 중복 작업을 하거나 서로 간섭하기 때문에, 원하는 대로 동작하도록 신경 써야 함  
    리스너가 하나라면 이런 복잡성이 사라짐  

- 브로드캐스트 큐
  대부분의 '이벤트' 시스템이 이런 식  
  리스너가 10개 있다면 이벤트가 하나 들어왔을 때 10 개의 리스너 모두가 그 이벤트를 볼 수 있음  
  - 이벤트가 무시될 수 있음  
    리스너가 하나도 없다면 어느 누구도 이벤트를 볼 수 없음  
    대부분의 브로드캐스트 시스템에서는 리스너가 없을 때 발생한 이벤트는 그대로 버려짐  

  - 이벤트 필터링이 필요할 수 있음  
    거의 모든 코드에서 브로드캐스트 큐에 접근할 수 있는 경우가 많고, 리스너도 많이 두는 편  
    그러다 보니 이벤트 개수 * 리스너 개수만큼 이벤트 핸들러가 자주 호출됨  

    이벤트 핸들러 호출 횟수를 줄이기 위해 대부분의 브로드캐스트 이벤트 시스템에서는 리스너가 받고 싶은 이벤트 집합을 조절할 수 있게 함  
    예를 들어 마우스 이벤트만 받고 싶다든지, 특정 영역 안에 있는 UI 이벤트만 받는다든지 하는 식  

- 작업 큐  
  브로드캐스트 큐와 마찬가지로 리스너가 여러 개 있음  
  차이점은 큐에 들어 있는 데이터가 리스너 중에서 **한곳**에만 간다는 점  
  스레드 여러 개가 동시에 실행 중인 스레드 풀에 작업을 나눠줘야 할 때 일반적으로 사용하는 패턴  
  - 작업을 분배해야 함  
    큐에 들어 있는 데이터가 하나의 리스너에만 가기 때문에 큐는 어느 리스너에 보내면 좋을지 알아야 함\

#### 누가 큐에 값을 넣는가?  
- 넣는 측이 하나라면  
  동기형 관찰자 패턴(4장)에 가장 가까운 형태  
  하나의 특정 객체에서만 이벤트를 만들 수 있고, 나머지는 이벤트를 받을 수만 있음  
  - 어디에서 이벤트가 오는지를 암시적으로 알음  
    큐에 값을 추가할 수 있는 객체가 하나밖에 없기 때문에, 모든 리스너는 누가 데이터를 보냈는지 안전하게 추측할 수 있음  

  - 보통 리스너가 여러 개  
    일대일 큐를 만들 수도 있지만 이러면 단순한 큐 자료구조에 가까움  

- 넣는 측이 여러 개라면  
  - 이벤트 순환을 주의 해야함  
    어디에서나 큐에 데이터를 넣을 수 있기 때문에 이벤트 처리 도중에 실수로 큐에 데이터를 넣기 쉬움  
    잘못하면 피드백 루프가 생길 수 있음

  - 이벤트를 보낸 객체에 대한 레퍼런스를 이벤트에 추가해야 할 필요가 있을 수 있음  
    이벤트는 누구나 보낼 수 있기 때문에 리스너가 이벤트를 받아도 누가 보냈는지를 알 수 없음  
    리스너에서 보낸 쪽의 정보가 필요하다면 나중에 리스너에서 사용할 수 있도록 이벤트 객체에 보낸 객체의 정보를 같이 넣어줘야 함  

#### 큐에 들어간 객체의 생명주기는 어떻게 관리할 것인가?  
동기형 알림에서는 받는 쪽에서 메시지 처리를 끝내기 전에는 리턴하지 않기 때문에 보내는 쪽에서 기다리고 있어야 함  
즉, 메시지는 스택에 들어가는 지역 변수이기만 해도 충분함  
큐에서는 큐에다 메시지를 추가하는 함수 호출이 끝난 후에도 메시지 객체가 유지되어야 함  
C나 C++에서는 객체 생명을 우리가 직접 관리해야 함  
- 소유권을 전달  
  가장 전통적으로 사용하는 방식  
  메시지가 큐에 들어가고 나면, 메시지의 소유권을 큐가 가져가고 보내는 쪽에서는 더 이상 메시지를 소유하지 않음  
  데이터를 처리할 때는 받는 쪽에서 메시지 소유권을 가져가고 메시지 해제도 해야 함  

- 소유권을 공유  
  메시지를 참조하는 곳이 어디 하나라도 있다면 계속 메모리에 남아 있다가 아무도 자신을 참조하지 않으면 알아서 해제됨  

- 큐가 소유권을 가짐  
  보내는 쪽에서는 메시지를 직접 생성하지 않고, 큐에 '새로운'메시지를 하나 달라고 요청  
  큐는 미리 할당해놓은 메시지의 레퍼런스를 반환하고 보내는 쪽에서는 여기에 값을 채움  
  처리가 끝나고 나면 받는 쪽에서 이 메시지를 참조

---
---
### CHAPTER 16. 서비스 중개자
---
#### 16.1, 16.2 의도, 동기
서비스를 구현한 구체 클래스는 숨긴 채로 어디에서나 서비스에 접근할 수 있게 함  

객체나 시스템 중에는 거의 모든 코드에서 사용되는 것들이 있음  
게임 코드 중에서 메모리 할당, 로그, 난수 생성을 쓰지 않는 곳은 찾아보기 어려움  
이런 시스템은 게임 전체에서 사용 가능해야 하는, 일종의 **서비스**라고 볼 수 있음  

```
// 정적 클래스를 쓸 수도 있고
AudioSystem::playSound(VERY_LOUD_BANG);

// 싱글턴을 쓸 수도 있음
AudioSystem::instance()->playSound(VERY_LOUD_BANG);
```
오디오 시스템을 쓸 때 둘 다 원하는 결과는 얻을 수는 있지만 강한 커플링도 함께 생김  
오디오 시스템을 접근하는 모든 곳에서 AudioSystem이라는 구체 클래스뿐만 아니라 정적 클래스 또는 싱글턴으로 만든 접근 메커니즘까지 직접 참조하게 됨  

물론 사운드 출력에는 어느 정도 커플링은 피할 수 없으나 위와 같은 방식은 우편물을 받기 위해 세계 모든 사람에게 주소를 알려주는 꼴과 같음  
이럴 때 주소가 바뀌기라도 하면 모든 사람들에게 다시 주소를 알려줘야하는 불편함이 있음  
이런 방식보다는 전화번호부 같이 호출하는 쪽에서 전화번호부를 통해 우리를 찾게 함으로써, **우리를 찾을 방법을 한 곳에서 편하게 관리**할 수 있음  

이게 서비스 중개자 패턴의 핵심  
서비스 중개자 패턴은 서비스를 사용하는 코드로부터 서비스가 누구인지(서비스를 구현한 구체 클래스 자료형이 무엇인지), 어디에 있는지(클래스 인스턴스를 어떻게 얻을지)를 몰라도 되게 해줌  

#### 16.3 패턴
**서비스**는 여러 기능을 추상 인터페이스로 정의함  
구체 **서비스 제공자**(service provider)는 이런 서비스 인터페이스를 상속받아 구현  
이와 별도인 **서비스 중개자**(service locator)는 서비스 제공자의 실제 자료형과 이를 등록하는 과정은 숨긴채 적절한 서비스 제공자를 찾아 서비스에 대한 접근을 제공

#### 16.4 언제 쓸 것인가?
무엇이든지 프로그램 어디에서나 접근할 수 있게 하면 문제가 생기기 쉬움  
**절제해서** 사용하는 게 좋음  

접근해야 할 객체가 있따면 전역 메커니즘 대신, 필요한 **객체를 인수로 넘겨줄** 수는 없는지부터 생각  
이 방법은 쉬우면서 커플링을 명확하게 보여줄 수 있음  
대부분은 이렇게만 해도 충분  

하지만 직접 객체를 넘기는 방식이 불필요하거나 도리어 코드를 읽기 어렵게 하기도 함  
로그나 메모리 관리 같은 시스템이 모듈의 공개 API에 포함되어 있어선 안됨  
렌더링 함수 매개변수에는 렌더링에 관련된 것만 있어야지 로그 같은게 섞여 있어서는 곤란  

또 어떤 시스템은 본질적으로 하나뿐  
대부분의 게임 플랫폼에는 오디오나 디스플레이 시스템이 하나만 있음  
이런 환경적인 특징을 여러겹의 메서드 계층을 통해서 가장 깊숙이 들어 있는 함수에 전달하는 것은 쓸데없이 복잡성만 늘리는 셈  

이럴 때 서비스 중개자 패턴으로부터 도움을 받을 수 있음  
이 패턴은 더 유연하고 더 설정하기 좋은 싱글턴 패턴  
잘만 사용하면 런타임 비용은 거의 들이지 않고도 코드를 훨씬 유연하게 만들 수 있음  
(반대로, 잘못 사용하면 싱글턴 패턴의 나쁜 점은 전부 다 있으면서 실행 성능까지 떨어짐)  

#### 16.5 주의사항  
서비스 중개자 패턴에서는 두 코드가 커플링되는 의존성을 런타임 시점까지 미루는 부분이 가장 어려움  
유연성은 얻을 수 있지만, 코드만 봐서는 어떤 의존성을 사용하는지를 알기 어렵다는 비용이 발생

- 서비스가 실제로 등록되어 있어야함  
  싱글턴이나 정적 클래스에서는 인스턴스가 **항상** 준비되어 있어야함  
  코드를 호출할 때 그 객체가 존재한다는 게 보장됨, 하지만 서비스 중개자 패턴에서는 서비스 객체를 등록해야 하기 때문에, 필요한 객체가 없을 때를 대비해야 함  

- 서비스는 누가 자기를 가져다가 놓은지 모름  
  서비스 중개자는 전역에서 접근 가능하기 때문에 모든 코드에서 서비스를 요청하고 접근할 수 있음  
  즉, 서비스는 어느 환경에서나 문제없이 작동해야 함  
  
  어떤 클래스를 게임 루프에서 시뮬레이션할 때만 사용해야 하고 렌더링 중에는 사용하면 안 된다면, 서비스로는 적합하지 않음  
  서비스는 정확히 정해진 곳에서만 실행되는 걸 보장할 수 없기 때문  
  어떤 클래스가 특정 상황에서만 실행되어야 한다면, 전체 코드에 노출되는 서비스 중개자 패턴은 적용하지 않는게 안전


---
> 본 내용은 한빛미디어에서 출판한 '게임 프로그래밍 패턴 : 더 빠르고 깔끔한 게임 코드를 구현하는 13가지 디자인 패턴' 을 읽고 공부하며 작성하였습니다.