# OS

간단하게 운영체제 공부

---

## 목차
1. 페이지 교체 방법
2. 프로세스 스케줄링
3. 디스크 스케줄링

---

## 1. 페이지 교체 방법

---

### OPT (Optimal policy)
앞으로 가장 오랫동안 사용하지 않을 페이지를 교체하는 방법  

성능은 가장 좋으나, 각 페이지의 호출 순서와 참조 상황을 미리 예측해야하기 때문에 실현 가능성이 희박

|참조 페이지  |2|3|2|1|5|2|3|5|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|페이지 프레임|**2**|2|*2*|2|2|*2*|2|2|
|            | |**3**|3|3|3|3|*3*|3|
|            | | | |**1**|**5**|5|5|*5*|
> 페이지 폴트 4번(굵은 글씨)

---

### FIFO (First-in, first-out)
페이지 교체시 물리적 메모리에 가장 먼저 올라온 페이지를 우선적으로 교체하는 방법  

페이지의 향후 참조 가능성을 고려하지 않고, 물리적 메모리에 들어온 순서대로 교체 대상을 선정하기 때문에 비효율적인 상황이 발생할 수 있음  

빌레이디의 모순 현상이 발생할 수 있음  
(페이지 프레임 수가 많으면 페이지 폴트 수가 줄어드는 것이 일반적이나,  
페이지 프레임 수를 증가시켰음에도 페이지 폴트가 더 많이 일어나는 현상)

|참조 페이지  |2|3|2|1|5|2|3|5|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|페이지 프레임|**2**|2|*2*|2|**5**|5|5|*5*|
|            | |**3**|3|3|3|**2**|2|2|
|            | | | |**1**|1|1|**3**|3|
> 페이지 폴트 6번(굵은 글씨)

---

### LRU (Least Recently Used)
최근에 가장 오랫동안 사용하지 않은 페이지를 교체하는 방법  
(마지막 참조시점이 가장 오래된 페이지 교체)  

시간지역성(temporal locality)의 성질을 고려  
(과거에 가장 오랫동안 참조되지 않은 것은 미래에도 참조되지 않을 가능성이 높음)

계수기(counter)나 스택(stack)과 같은 별도의 하드웨어가 필요하며, 시간적인 오버헤드가 발생함  

성능이 좋고 구현 가능하나 오버헤드가 너무 커서 잘 사용하지 않음  

|참조 페이지  |2|3|2|1|5|2|3|5|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|페이지 프레임|**2**|2|*2*|2|2|*2*|2|2|
|            | |**3**|3|3|**5**|5|5|*5*|
|            | | | |**1**|1|1|**3**|3|
> 페이지 폴트 5번(굵은 글씨)

---

### LFU (Least Frequently Used)
사용 빈도가 가장 적은 페이지를 교체하는 방법  

LRU보다 오랜시간 동안의 참조 기록을 반영할 수 있다는 장점  

단, 시간에 따른 페이지 참조 변화를 반영하지 못하고, LRU보다 구현이 복잡하다는 단점이 있음  

|참조 페이지  |2|3|2|1|5|2|3|5|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|페이지 프레임|**2**|2|*2*|2|2|*2*|2|2|
|            | |**3**|3|3|**5**|5|5|*5*|
|            | | | |**1**|1|1|**3**|3|
> 페이지 폴트 5번(굵은 글씨)

---

### 클럭 알고리즘
Not Recently Used (NRU), Not Used Recently(NUR)로 불리기도 함  

LRU와 비슷한 방법, 최근에 사용하지 않은 페이지를 교체하는 방법  

LRU는 가장 오래전에 참조된 페이지를 교체하는 것에 비해, NUR은 오랫동안 사용하지 않은 페이지 중 하나를 교체  

최근의 사용 여부를 확인하기 위해서 각 페이지마다 참조 비트와 변형 비트를 사용  

 - 참조 비트(Reference Bit) : 페이지가 호출되지 않았을 때는 0, 호출되었을 때는 1로 저장(처음 메모리 로드시 1)
 - 변형 비트(Modified Bit, Dirty Bit) : 페이지 내용이 변경되지 않았을 때는 0, 변경되었을 때는 1로 저장

 적절한 구현 난이도와 적절한 성능으로 주로 사용됨  

 |참조 비트|변형 비트|교체 순서|
|:-:|:-:|:-:|
|0|0|1|
|0|1|2|
|1|0|3|
|1|1|4|

---

## 2. 프로세스 스케줄링

---

프로세스 스케줄링은 다음 네 가지 경우에 일어날 수 있음
 - 프로세스가 실행 상태(running)에서 대기 상태(waiting)으로 전환될 때
 - 프로세스가 실행 상태(running)에서 준비 상태(ready)으로 전환될 때
 - 프로세스가 대기 상태(waiting)에서 준비 상태(ready)으로 전환될 때
 - 프로세스가 종료되었을 때(terminated)

새로운 프로세스가 들어왔을 때 CPU를 뺏냐 안 뺏냐의 차이로 비선점형과 선점형으로 나뉨

 - Non-preemptive(비선점형)  
    - 어떤 프로세스가 CPU를 할당 받으면 그 프로세스가 종료되거나 입출력 요구가 발생하여 자발적으로 중지될 때까지 실행되도록 보장함  
    - 순서대로 처리되는 공정성이 있고, 다음에 처리해야 할 프로세스와 관계 없이 응답 시간을 예상할 수 있음  
    - 스케줄러 호출 빈도가 낮고 문맥교환에 의한 오버헤드가 적음  
    - 일괄처리에 적합  
    - CPU 사용 시간이 긴 하나의 프로세스가 CPU 사용기간이 짧은 여러 프로세스를 오랫동안 대기시킬 수 있으므로, 처리율이 떨어질 수 있다는 단점이 있음  

 - Preemptive(선점형)  
    - 어떤 프로세스가 CPU를 할당받아 실행중에 있어도 다른 프로세스가 실행중인 프로세스를 중지하고 CPU를 강제로 점유  
    - 모든 프로세스에게 CPU 사용 시간을 동일하게 부여할 수 있음  
    - 빠른 응답시간을 요하는 대화식 시스템에 적합
    - 운영체제가 프로세서 자원을 선점하고 있다가 각 프로세스의 요청이 있을 때 특정 요건들을 기준으로 자원을 배분하는 방식

프로세스의 우선순위 변동 여부에 따라 정적 스케줄링과 동적 스케줄링으로 구분

 - 정적 스케줄링(Static Scheduling) : 프로세스가 부여된 우선순위가 바뀌지 않음
 - 동적 스케줄링(Dynamic Scheduling) : 스케줄링 과정에서 프로세스의 우선순위를 변동시킴

---

### 비선점 스케줄링

---

#### FCFS (First Come First Serve Scheduling)
CPU를 먼저 요청한 프로세스가 먼저 CPU를 배정

#### SJF (Short Jop First)
작업시간이 가장 짧은 순서대로 자원을 할당  
따라서 실행시간이 긴 프로세스는 무한대기에 빠질 수 있음

#### HRRN (Highest Response-ratio Next)
SJF의 단점을 보완한 방법으로 우선순위 알고리즘으로 계산한 값이 큰 프로세스부터 실행  
`우선순위 = (실행시간 + 대기시간) / 대기시간`

---

### 선점 스케줄링

---

#### 기한부
일정시간동안 프로세스가 완료되지 않으면 삭제하고 다시 실행

#### 우선순위
특정 규칙에 따라 미리 프로세스에 우선순위를 매겨서 그 순서대로 CPU자원을 할당  
SJF와 동일하게 낮은 우선순위를 받으면 무한대기에 빠질 수 있음  
시분할 시스템에서 사용하는 방식

#### RR (Round Robin)
프로세스의 종료 여부와 관계없이 일정시간 간격으로 돌아가면서 모든 프로세스를 실행  
Time Sharing System과 같음  
시간간격이 짧으면 문백교환이 자주 일어나고, 오버헤드가 자주 발생

#### SRT (Shortest Remaining Time)
실행중인 프로세스의 남은시간보다 대기중인 프로세스의 실행시간이 더 짧으면 대기중인 프로세스에게 자원을 할당

#### 다단계 큐
프로세스들을 특정 그룹으로 분류해서 다양한 스케줄링 방법을 사용하는 여러 작업큐로 할당

#### 다단계 피드백 큐
다단계 큐에서 큐에 있는 프로세스가 다른 큐로 이동할 수 있도록 기능을 추가한 것

---

## 3. 디스크 스케줄링

---

### FIFO(First-in, first-out)
본질적으로는 공평해보이나 빠른 서비스를 제공하지 못함  

 - 장점 : 알고리즘이 단순, 공평하게 요청 처리 
 - 단점 : 비용이 많이 발생, 비효율적

`queue : 98, 183, 37, 122, 14, 124, 65, 67, current head starts at : 53`
![99AB873C5BDF03AF03](https://user-images.githubusercontent.com/32252062/67928868-674f6700-fbff-11e9-8bd2-fcffe7dae3f7.png)

### SSTF(Shortest Service Time First)
현재 헤드에서 가장 가까운 트랙의 요청을 먼저 처리  
즉, 현재 헤드셋을 처리하고, 다음 요청 중에 이동거리가 가장 적은거리에 있는 트랙을 처리  

 - 장점 : Seek Time이 적음(트랙을 찾는 시간 최소화, 처리량을 극대화)  
 - 단점 : 안쪽 및 바깥쪽에 있는 요청들을 기아 현상이 발생할 수 있음, 또한 응답 시간 편차가 큼

`queue : 98, 183, 37, 122, 14, 124, 65, 67, current head starts at : 53`
![99F8B13C5BDF03AF0B](https://user-images.githubusercontent.com/32252062/67928885-6fa7a200-fbff-11e9-9529-10eccd70a98f.png)

### SCAN
헤드셋의 진행방향에 있는 요청을 처리하고, 다시 반대 방향으로 틀어 반대방향에 있는 요청들을 처리  
엘레베이터 기법이라고도 함  

 - 장점 : SSTF의 바깥쪽 트랙의 기아 가능성을 제거할 수 있고, 응답시간의 편차를 줄일 수 있음
 - 단점 : 양 쪽 끝 트랙이 가운데 위치한 트랙보다 대기 시간이 길어짐

`queue : 98, 183, 37, 122, 14, 124, 65, 67, current head starts at : 53`
![image](https://user-images.githubusercontent.com/32252062/67929116-eba1ea00-fbff-11e9-8646-880099aada68.png)

### C-SCAN
항상 한쪽 방향에서 반대방향으로 진행하며 트랙의 요청을 처리  
SCAN의 변형된 형태로 조금 더 시간을 균등하게 배분 가능

 - 장점 : 응답시간의 편차가 매우 적음, SCAN보다 시간균등성이 좋음
 - 단점 : 안쪽이나, 바깥쪽으로 처리할 요청이 없어도 헤드셋이 끝까지 이동하기 때문에 비효율적

`queue : 98, 183, 37, 122, 14, 124, 65, 67, current head starts at : 53`
![image](https://user-images.githubusercontent.com/32252062/67929263-3f143800-fc00-11e9-8a6e-cff3e5ec758c.png)

### LOOK, C-LOOK
SCAN과 C-SCAN을 보완하기 위한 스케줄링 기법  
요청 진행방향에서 더 이상 요청이 없다면, 끝단까지 가지 않고 처리

 - 장점 : 불필요한 헤드 이동시간을 제거
 - 단점 : 끝단까지 가야할지 말아야할지 판단하는데 있어서 오버헤드 발생

LOOK  
`queue : 98, 183, 37, 122, 14, 124, 65, 67, current head starts at : 53`
![image](https://user-images.githubusercontent.com/32252062/67929383-8ac6e180-fc00-11e9-9a39-761aa609275a.png)

C-LOOK  
`queue : 98, 183, 37, 122, 14, 124, 65, 67, current head starts at : 53`
![image](https://user-images.githubusercontent.com/32252062/67929397-91edef80-fc00-11e9-934f-dfe059c20a6b.png)

### N-step SCAN
SCAN 기법을 기초로 두며, 시작하기 전 대기하고 있는 요청들을 우선적으로 처리하고,  
처리하는 과정 중에 요청이 들어오는 것은 이후에 모아서, 반대방향으로 진행할 때 서비스  

장점  
 - SSTF, SCAN보다 응답시간의 편차가 적음  
 - 특정 방향에서의 많은 요청으로 인해 반대방향에서 들어온 요청들에 대한 기아현상을 방지할 수 있음

### SLTF(Shorteset Latency Time First)
회전 지연 시간을 최적화 하기 위한 대표적인 기법  
디스크 헤드가 특정 실린더에 도착하면 그 실린더 내의 여러 트랙에 대한 요청을 검사한 후  
회전 지연시간이 가장 짧은 요청부터 서비스  
헤드의 이동이 거의 없어, 고정 헤드 장치인 드럼에 사용