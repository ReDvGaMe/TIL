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


---
> 본 내용은 한빛미디어에서 출판한 '게임 프로그래밍 패턴 : 더 빠르고 깔끔한 게임 코드를 구현하는 13가지 디자인 패턴' 을 읽고 공부하며 작성하였습니다.