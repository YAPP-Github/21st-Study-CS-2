## CPU 스케줄링

### 프로세스와 멀티프로그래밍

하나의 CPU에는 여러 프로세스가 동시에(concurrent) 돌아간다. 이러한 멀티프로그래밍 환경에서는 CPU 이용률을 최대화하는 것은 운영체제 설계의 핵심이 된다. 

<img width="810" alt="image" src="https://user-images.githubusercontent.com/67777523/205054567-b3f16798-f7c2-450b-b9a8-f7d7f15c8e07.png">


- 프로세스의 상태
    - **New** : 생성된 상태
    - **Running** : CPU를 점유하여 실행되고 있는 상태
    - **Waiting** : 실행 중(running)에 IO나 event wait 같은 인터럽트 발생 시 waiting 상태로 대기. 작업이 끝나지 않았기에 ready에서 CPU 할당을 기다리는 것이 아님. IO, event가 끝나면 ready 상태로 가서 CPU 할당을 기다린다.
    - **Ready** : CPU 점유할 준비가 다 되었으면 ready 상태에서 대기. ready에서 CPU를 점유해 running 상태로 가는 것을 schedulter **dispatch**라고 함. (스케쥴러가 CPU를 디스패치 해준다)
    - **Terminated** : running에서 리턴을 한다던가 exit하게되면 terminated 상태가되어 리소스를 해제한다.
- 프로세스의 상태 전이
    - **Admitted** : 프로세스 생성이 가능하여 승인되었다.
    - **Scheduler Dispatch** : 준비 상태에 있는 프로세스 중 하나를 선택해 실행한다.
    - **Interrupt** : 예외, 입출력, 이벤트 등이 발생하여 현재 실행 중인 프로세스를 Ready 상태로 바꾸고, 해당 작업을 수행한다.
    - **I/O or Event wait** : 실행 중인 프로세스가 입출력이나 이벤트를 처리해야 하는 경우, 입출력이나 이벤트가 끝날 때까지 Waiting 상태에 머무른다.
    - **I/O or Event completion** : 입출력이나 이벤트가 끝나면 프로세스를 다시 Ready 상태로 전환하여 CPU 할당을 기다린다.

### CPU - I/O Burst Cycle

프로세스의 실행은 CPU burst(CPU 실행)과 I/O burst(I/O 대기)의 사이클로 구성된다.

<img width="709" alt="image" src="https://user-images.githubusercontent.com/67777523/205054624-c29690a2-210e-4f52-aa50-d0b69d77d170.png">


### CPU 스케쥴링

CPU 스케쥴링이란 작업을 처리하기 위해 프로세스에게 CPU를 할당하기 위한 정책을 계획하는 것이다. **CPU가 유휴 상태가 될 때마다 OS는 Ready Queue에 있는 프로세스 중 하나를 선택해 실행**한다. **실행할 프로세스는 CPU 스케쥴러에 의해 결정**된다. CPU 스케쥴러는 실행 준비가 되어 있는 프로세스 중 하나를 선택해 CPU를 할당한다. 스케쥴링 정책에 따라 큐를 정렬하고 앞에 있는 프로세스부터 CPU를 주게 된다. 일반적으로 큐에는 프로세스의 정보가 담긴 PCB가 들어있다.

CPU 스케쥴링은 다음과 같은 상황에 발생한다.

1. 한 프로세스가 실행 상태에서 대기 상태로 전환 (I/O 발생)
2. 프로세스가 실행 상태에서 준비 완료 상태로 전환 (인터럽트 발생)
3. 프로세스가 대기 상태에서 준비 완료 상태로 전환 (I/O 종료)
4. 프로세스 종료

### 선점 / 비선점 스케쥴링

- 1, 4 : non-preemptive
- 2, 3 : preemptive or non-preemptive

**선점 preemptive**

운영체제가 프로세스의 **CPU를 강제로 회수**할 수 있다. 시분할 시스템에서 타임 슬라이스가 끝났거나, 인터럽트나 시스템 호출 종료 시 우선순위가 더 높은 프로세스가 발생되었음을 알았을 때 등 이러한 상황에서 실행 프로세스의 CPU를 강제로 회수한다. 

프로세스 상태 전이 - Interrupt, I/O or Event completion, I/O or Event wait, Exit

**비선점 non-preemptive**

한 프로세스에 CPU가 할당되면 프로세스가 종료되거나 대기 상태로 전환되어 **CPU를 방출할 때까지 점유**한다. 즉 프로세스 종료나 I/O와 같은 이벤트 발생 시까지 실행을 보장한다.

프로세스 상태 전이 - I/O or Event wait, Exit 

### Dispatcher

디스패처는 **CPU 스케쥴러가 선택한 프로세스에게 CPU 제어권을 준다**.

- 한 프로세스에서 다른 프로세스로 Context Switching
- 사용자 모드로 전환
- 프로그램을 재시작하기 위해 사용자 프로그램의 적절한 위치로 jump하는 일

**Dispatch latancy** : 디스패처가 하나의 프로세스를 정지하고, 다른 프로세스가 시작하기까지 소요되는 시간

### Scheduling Criteria

CPU를 효율적으로 사용하기 위해서는 CPU를 할당하는 순서와 방식을 결정해야 한다. CPU 스케쥴링 알고리즘 중 하나를 선택하기 위한 비교 기준은 다음과 같다.

- **CPU 이용률**(CPU utilization) : 일정 기간 또는 특정 스냅샷에서의 CPU 이용률
- **처리량**(Throughput) : 단위 시간 당 완료되는 프로세스 수
- **총 처리 시간**(Turnaround Time) : 프로세스 제출 시각과 완료 시각 사이의 간격
- **대기 시간**(Waiting Time) : 프로세스가 ready queue에서 대기한 시간의 합
- **응답 시간**(Response Time) : 하나의 request 제출 후 첫 번째 response가 나올 때까지의 시간

CPU 이용률, 처리량을 최대화하고, 총 처리 시간, 대기 시간, 응답 시간을 최소화하는 알고리즘을 택한다.

**시스템 별 중요 기준**

대부분의 알고리즘은 trade-off 이고, 시스템에 따라 중요하게 생각하는 criteria가 다르기에 컨텍스트에 따라 선택을 해야한다.

- Batch System
    
    한 번에 하나의 프로그램을 수행하는 시스템으로, 최대한 많은 일을 수행하기 위해서 throughput과 CPU utilization이 중요하다.
    
- Interactive System
    
    사용자와 interactive하게 동작하려면 Response time과 Waiting time을 최소화하고, 사용자가 요구하는 바를 이룰 수 있는 것이 중요하다.
    
- Real-time System
    
    데드라인이 정해진 시스템으로, 데드라인 내에 완료하는 것이 중요하다.
    

### 스케쥴링 알고리즘

**FCFS (First Come First Served)**

큐에 도착한 순서대로 CPU를 할당한다.

- 개발이 용이
- 비선점형 알고리즘으로 한 프로세스에 CPU가 할당되면, 프로세스가 종료되거나 I/O 처리를 요구하여 CPU를 방출하기 전까지 CPU를 점유한다.
- 공평성을 유지할 수 있다. Starvation 발생하지 않음.
- 실행 시간이 긴 프로세스가 먼저 도착하면 실행 시간이 짧은 작업도 전부 대기해야 한다.

**SJF (Shortest Job First)**

수행 시간이 가장 짧은 작업을 우선적으로 수행한다. 

- 평균 대기 시간이 짧아진다.
- Starvation 발생. 실행 시간이 긴 프로세스는 영원히 CPU를 할당 받지 못할 수 있다.
- 새로운 프로세스가 들어올 때 next CPU burst time을 알 수 없다. 즉 shortest job을 판단할 수 없다. 
→ next CPU burst time을 예측한다.
    
    과거 CPU burst의 길이로부터 exponential average를 구한다. 
    
    최근 값에 가중치를 높이고, 오래된 값에 가중치를 낮게 준다.
    
- SJF는 이론적으로 optimal한 방법이지만 현실적으로 모든 과거 cpu burst time을 기록해두고 next cpu burst time을 예측하는 것은 어렵다.
- Preemptive vs non-preemptive
    
    이론적으로는 선점형이 유리하지만 매번 프로세스가 들어올 때마다 남은 cpu burst time을 계산해서 짧은 것을 찾아서 넘기도록 구현하는 것은 쉽지 않다.
    

**SRTF (Shortest Remaining Time First)**

현재 수행 중인 프로세스의 남은 burst time보다 더 짧은 CPU burse time을 가진 새로운 프로세스가 도착할 경우 CPU를 강제로 회수한다. 

- Preemptive SJF
- 새로운 프로세스가 도달할 때마다 스케쥴링을 한다.
- Starvation 발생할 수 있다.

**HRN (Highest Response-ratio Next)**

우선 순위를 계산해서 우선 순위가 높은 프로세스에게 CPU를 할당한다.

$우선 순위 = (대기시간 + 실행시간)/(실행시간)$

대기 시간이 길어지면 우선순위가 커지기에 실행 시간이 긴 프로세스도 CPU가 할당될 수 있다.

**RR (Round Robin)**

FCFS 스케쥴링과 유사하지만 FCFS와 달리 선점형이다. Time quantum 또는 time slice라고 하는 작은 단위의 시간을 정하고, 스케쥴러는 ready queue에 있는 프로세스를 순서대로 단위 시간 동안 CPU를 할당한다. 

- 단위 시간이 크면 FCFS와 동일하다.
- 단위 시간이 작다면 context switching이 증가해 오버헤드가 커진다.

**Priority-based**

각 프로세스의 우선순위에 따라 CPU를 할당한다. 

- indefinite blocking
    
    실행 준비는 되어 있으나 CPU를 사용하지 못하는 프로세스가 CPU를 무한정 기다리는 상태
    
- starvation
    
    높은 우선순위의 프로세스가 계속 들어와서 낮은 우선순위의 프로세스는 CPU를 얻지 못하는 상태
    

해결 방법

- Aging
    
    오래 기다린 프로세스의 우선순위를 점진적으로 증가시킨다.
    
- 라운드 로빈 스케쥴링과 결합

**Multilevel Queue**

작업들을 여러 그룹으로 나눠서 여러 개의 큐를 사용한다. 큐 마다 적절한 스케쥴링 알고리즘을 사용할 수 있다. 우선순위가 높은 큐가 완료되어야(비어야) 다음 우선순위를 가진 큐가 돌아가므로 낮은 우선순위의 큐는 실행되지 못할 수 있다.

<img width="770" alt="image" src="https://user-images.githubusercontent.com/67777523/205055045-37434c39-9ded-4757-9f7e-8afbd25c618e.png">


**Multilevel Feedback Queue**

Multilevel queue는 프로세스가 시스템 진입 시에 하나의 큐에 할당된다. 하지만 Multilevel Feedback Queue는 프로세스가 큐들 사이를 이동할 수 있다. CPU를 많이 사용하는 것은 점점 더 큰 quantum을 할당해 처리할 수 있도록 하고, CPU를 짧게 사용하는 프로세스는 빠르게 완료하고 빠져나갈 수 있다.

높은 우선순위 큐에서 실행되다가 완료되지 못하면 다음 우선순위의 큐로 넘어가고, aging으로 낮은 우선순위 큐에 있는 프로세스가 높은 우선순위로 올라갈 수도 있다. 

<img width="728" alt="image" src="https://user-images.githubusercontent.com/67777523/205055099-a83b6ff0-1703-4c52-9222-8f964f493326.png">


- Aging과 Starvation을 예방한다.

---

## Thread Scheduling

### 경쟁 범위

현대의 운영체제는 쓰레드를 지원하기에 실제로는 프로세스가 아닌 커널 쓰레드를 스케쥴링한다. 

쓰레드에는 커널 쓰레드와 유저 쓰레드가 있다. 커널 쓰레드는 커널 영역에서 쓰레드 연산을 수행하며 커널이 관리한다. 유저 쓰레드는 사용자 영역에서 쓰레드 연산을 수행하고, 쓰레드 라이브러리가 관리한다. 커널은 관여하지 않는다.

- **프로세스 경쟁 범위(Process Contention Scope, PCS)**
    
    동일한 프로세스 내 쓰레드 간 경쟁한다. 하나의 커널 쓰레드를 두고 여러 개의 유저 쓰레드가 경쟁한다.
    
- **시스템 경쟁 범위(System Contention Scope, SCS)**
    
    시스템 내에서 커널 쓰레드들 간 경쟁 범위. 하나의 CPU를 두고 여러 개의 커널 쓰레드가 경쟁한다.
    

### Pthread 스케쥴링

- PTHREAD SCOPE PROCESS : PCS 스케쥴링으로 쓰레드 스케쥴
- PTHREAD SCOPE SYSTEM : SCS 스케쥴링으로 쓰레드 스테쥴

---

## 멀티 프로세서 스케쥴링 Multiple-Processor

여러 개의 CPU를 사용하는 경우 여러 개의 쓰레드가 병렬적으로 실행될 수 있어 부하 공유(load sharing)이 가능해진다. 

### 멀티프로세서 시스템에서의 CPU 스케쥴링

- 비대칭 다중 처리 (AMP)
    
    모든 스케쥴링 결정과 I/O 처리 등을 하나의 프로세서(마스터 서버)가 처리하고, 나머지 프로세서는 사용자 코드만 실행한다.
    
    - 시스템 자료구조에 하나의 프로세서만 접근하므로 데이터 공유를 배제하여 간단하다.
    - 마스터 서버가 시스템 성능을 저하시키는 병목이 될 수 있다.
- 대칭 다중 처리 (SMP)
    
    개별 프로세서가 스스로 스케쥴링한다. 
    
    - 공유하는 공통 ready 큐를 갖는다.
        
        경쟁 조건이 발생할 수 있고, 다른 프로세서가 동일한 쓰레드를 스케쥴링 하지 않도록 해야한다.
        
    - 각 프로세서가 고유 쓰레드 큐를 갖는다.
        
        큐 공유로 인한 성능 저하가 없다. 하지만 큐마다 부하의 양이 다르므로 부하를 균등하게 하는 균형 알고리즘이 필요하다.
        

### 멀티코어 프로세서 Multicore processor

현대 컴퓨터 하드웨어는 **하나의 물리적인 칩 내에 여러 개 코어를 장착**하는 multicore 프로세서이다. 운영체제 입장에서는 각각이 **개별적인 논리적 CPU처럼** 보이게 된다. 

하나의 코어에 2개 이상의 하드웨어 쓰레드가 할당되는 멀티 쓰레드 처리 코어도 있다.

예를 들어 하나의 프로세서에 4개의 컴퓨팅 코어가 있고, 각 코어에 2개의 하드웨어 쓰레드가 있다면 운영체제 관점에서는 8개의 논리적 CPU가 있는 것처럼 보인다.

<img width="700" alt="image" src="https://user-images.githubusercontent.com/67777523/205055166-7babebd8-a550-43a4-a03c-c478cfbd6629.png">


Multithreading 방법

- 거친 멀티쓰레딩 Coarse-grained multithreading
    
    코어에서 하나의 쓰레드가 실행하다가 긴 지연시간을 가진 이벤트 발생 시 다른 쓰레드를 실행한다.
    
- 세밀한 멀티쓰레딩 Fine-grained multithreading
    
    명령어 주기의 경계와 같이 좀 더 세밀한 단위로 쓰레드 교환이 일어난다.
    

### Load Balancing

앞서 다룬 대칭 다중 처리 (SMP) 시스템에서 여러 프로세스를 최대한 활용하려면 부하를 모든 프로세서에게 균등하게 배분하는 것이 중요하다.

- Push migration
    
    특정 태스크가 주기적으로 각 프로세서의 부하를 검사하고 불균형 상태라면 과부하 프로세서에서 부하가 적은 프로세서로 쓰레드를 push 하여 부하를 분배한다.
    
- Pull migration
    
    쉬고 있는 프로세서가 바쁜 프로세스를 기다리고 있는 프로세스를 pull한다.
    

### Processor affinity

프로세서에서 다른 프로세서로 쓰레드를 이동하는 과정에는 캐시 무효화 및 디시 채우는 비용이 들기에 SMP를 지원하는 대부분의 운영체제는 쓰레드를 이주하지 않고 같은 프로세서 내에서 처리하려고 한다. 즉 프로세스는 현재 실행 중인 프로세서에 대한 선호도를 보인다.

Load balancing은 쓰레드를 한 프로세서에서 다른 프로세서로 이동하여 부하를 조정하므로 processor affinity의 장점을 상쇄한다. trade-off 관계

### Heterogeneous Multiprocessing

클록 속도나 전력 관리 측면에서 차이가 나는 코어를 사용하는 시스템을 말한다. 작업의 요구에 따라 특정 코어에 작업을 할당하여 전력 소비를 더 잘 관리할 수 있다. 

낮은 성능으로 오래 실행되어야 하는 작업을 little core에 할당하고, 짧은 시간 동안 고성능을 요구하는 작업은 big core에 할당한다. 

---

## Real-Time CPU Scheduling

- soft real-time system
    
    데드라인까지 태스크가 완료됨을 보장하지 않는다. 중요한 프로세스가 덜 중요한 프로세스 보다 우선적으로 스케쥴링 된다는 것만 보장한다.
    
- hard real-time system
    
    데드라인까지 완료됨을 보장한다. 
    

### Minimizing latency

실시간 태스크를 수행하려면 지연시간을 최소화하는 것이 중요하다.

- 인터럽트 지연시간
    
    인터럽트가 발생한 시점부터 해당 인터럽트 처리 루틴(ISR)이 시작하기까지의 시간
    
<img width="636" alt="image" src="https://user-images.githubusercontent.com/67777523/205055219-df2cf452-8a35-4afb-8cd8-580761bdd7f8.png">

    
- 디스패치 지연시간
    
    스케쥴링 디스패처가 하나의 프로세스를 멈추고 다른 프로세스를 시작하는 데까지 걸리는 시간
    
    디스패치 지연시간을 최소화하는 가장 효과적인 방법은 선점형 커널을 사용하는 것이다. 
    
<img width="586" alt="image" src="https://user-images.githubusercontent.com/67777523/205055268-d4c5ffd5-a6a2-4517-ac8c-112b37e0ea2f.png">

    

**실시간 CPU 스케쥴링**

- Priority Based Scheduling
- Rate Monotonic Scheduling
- Earliest Deadline First Scheduling
- Proportionate Share Scheduling
- POSIX Real Time Scheduling
