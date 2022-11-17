### Process

프로세스란 실행 중인 프로그램이다. OS의 작업 단위가 프로세스이고, OS의 주요한 역할이 프로세스를 관리하는 것이다.

OS가 프로세스를 관리하려면 각 프로세스에 대한 정보가 필요하다. 프로세스의 특징은 프로세스 메타데이터를 통해 알 수 있고, 메타데이터는 PCB(Process Control Block)에 저장된다.

**Process Metadata**

- Process ID
- Process State : 프로세스의 상태(new, running, waiting, ready, terminated)
- CPU Registers : PC(Program Counter), IR(Instruction Register), …
- CPU scheduling information
- Memory management information
- Accounting information
- I/O status information

### PCB(Process Control Block)

PCB에는 프로세스 메타데이터가 저장된다. CPU에서는 한 프로세스가 시작부터 종료까지 수행되는 것이 아니라 프로세스의 상태에 따라 교체 작업이 이루어진다. 현재 수행 중인 프로세스에서 다른 프로세스로 교체될 때 현재 프로세스의 정보를 어딘가에 저장하고 있어야 이 프로세스를 다시 수행할 때 이전 작업을 이어서 할 수 있다. 이러한 정보를 저장하는 곳이 PCB이다. 예를 들어 인터럽트가 발생해 running 상태의 프로세스가 교체될 때 프로세스에 관한 정보를 PCB에 저장해야 한다.

프로세스가 생성되면 메모리에 해당 프로세스의 PCB가 함께 생성되고, 프로세스가 완료되면 제거된다. 

**PCB 관리 방식**

PCB는 Linked List 방식으로 관리된다. PCB List Head에 PCB가 생성될 때마다 하나씩 붙게 된다. Linked List의 특성상 삽입 삭제가 용이하다. 

### Context Switching

수행 중인 프로세스를 변경하는 작업을 Context Switching이라고 한다. CPU가 현재 프로세스의 상태를 PCB에 보관하고, 새로운 프로세스의 정보를 PCB에 읽어(복원) 레지스터에 적재하는 과정이다. Context Switching에서 컨텍스트란 CPU가 해당 프로세스를 실행하는데 필요한 프로세스 정보로, PCB의 내용이 컨텍스트라고 볼 수 있다. Context Switching을 통해 멀티 프로세싱, 멀티 스레딩이 가능해진다.

**Context Switching이 발생하는 상황**

- I/O 인터럽트
- CPU 사용 시간 만료(time slice expired)
- 자식 프로세스 생성(fork)
- 인터럽트 처리 대기

**Context Switching 과정**

![image](https://user-images.githubusercontent.com/67777523/202478250-6c4f5071-e185-4b59-a266-50dd74c9a646.png)


1. P0 실행 중
2. 인터럽트나 시스템 콜 발생
3. PCB0에 현재 상태 저장
4. PCB1의 상태를 reload 한다. 이제 P1이 CPU 제어권을 갖는다.
5. P1 실행

**Context Switching Overhead**

Context Switching을 하는 동안에는 CPU가 다른 작업을 할 수 없고, 꽤 비용이 드는 작업이다. Context Switching이 너무 빈번하게 발생하면 오버헤드가 커져 성능이 악화될 수 있다. 하지만 프로세스 실행 중 입출력과 같은 이벤트 발생 시 CPU가 그냥 놀게 두는 것보다는 다른 프로세스를 실행시키는 것이 효율적이기에 오버헤드를 감수하고 Context Switching을 한다.

