# 페이지와 세그먼테이션 (2)

1. 메모리 공유와 페이지
2. page table entry 
3. page table imlementations 
4. segmentation

## 1. 메모리 공유와 페이지

- 프로세스간 중복된 내용을 메모리에 올릴 필요가 없어서, read - only 영역은 공유하는 것이 좋다.
- 메모리 공유는 page를 이용할 때만 가능하다.
    
    ![image](https://user-images.githubusercontent.com/30929671/203714497-ebc20fe1-0508-4638-a01c-de0972a94e82.png)
    
    페이지 테이블을 통해 동일한 프레임을 가리키기만 하면 된다. 구체적으로는 동일한 프로그램을 사용할 경우 fork()를 한 다음, 데이터의 변경이 있어 달라지는 부분만 복사한다. 
    
- Contiguous allocation에서 공유가 불가능한 이유

  ![image](https://user-images.githubusercontent.com/30929671/203714584-59b874cc-cf5e-4515-883a-79b1fa363eb7.png)

  contiguous allocation


영역을 공유하기 위해서는 주소공간의 시작위치가 겹쳐버린다. 그럴 경우 자연스럽게 writable한 영역도 겹쳐버리기 때문에 다른 프로세스에 의해 데이터가 덮어씌워진다. (페이지를 사용하면 겹치는 writable 영역에서는 새로운 페이지 만들면 된다)

## 2. Page Table Entries

페이지 번호

프레임 번호

valid-invalid bit

- 페이지 폴트와 관련

protection bit(or rwx)

- 페이지 보호 기능을 위해 존재한다.
- 한개의 비트로 구현하거나, 3개의 비트로 rwx 권한을 설정할 수 있음.

reference bit

- cpu의 페이지 접근 여부

dirty bit (modified bit)

- 내용이 변경된 적이 있는 경우 표시
- swap-out 시에 디스크에 변경사항 기록

## 3. Page Table Implementations

1. Hierarchical Paging
2. Hashed Page Tables
3. Inverted Page Tables

### Hierarchical Paging

- 페이지 테이블은 작지 않다.
    - 모든 페이지테이블 엔트리를 메모리에 두는 것은 메모리 낭비이다.
    - 특히 이것을 연속으로 할당하는 것은 큰 부담임
        - 왜 연속으로 할당해야 할까요?
- 계층적 페이징
    
    ![image](https://user-images.githubusercontent.com/30929671/203714754-c11071a7-ca61-4188-84a5-af5182f62c65.png)
    
    - 페이지 테이블을 n계층으로 나누어 관리한다. outer page table은 반드시 메모리에 적재해야 하지만, 나머지의 경우 주요한 **페이지 테이블 엔트리만( ≠ 페이지만)** 메모리에 적재하면 된다.
    - 메모리에 여러번 접근하는 문제점이 발생하지만, TLB hit rate이 워낙 높아서 큰 상관 없다. (요즘 64비트 OS는 4레벨 씀)
    - DB 인덱스, 파일 시스템의 indirect inode와 유사한 구조

### hashed page table

- 페이지 테이블은 search가 목적인 자료구조
- hash 사용시 O(1)에 처리 가능
- Collision 발생시 O(N)의 서치 코스트 발생 → 체이닝을 최소화 시키도록 해시함수를 디자인해야함.

![image](https://user-images.githubusercontent.com/30929671/203714796-9098e2a6-6fa2-4231-a138-83dcac6f1c8c.png)

### Inverted Page table

- 프로세스마다 별도로 페이지 테이블을 갖는 문제를 해결하기 위한 방법으로 하나의 system-wide page table을 갖는다.
- 페이지 테이블이 로지컬 페이지가 아닌 피지컬 페이지에 의존하여 구성된다.
    - 기존 페이지 테이블은 로지컬 페이지 하나에 페이지 테이블 엔트리 하나가 필요했다.
        - 논리적 주소공간을 표현하다 보니, 프로세스 10개만 띄워도 40GB를 표현할 수 있는 페이지 테이블이 필요했음.
    - 피지컬 프레임 하나에 페이지 테이블 엔트리 하나를 매핑시켜서 로지컬 페이지 개수와 관계 없이 엔트리를 구성한다.
    

![image](https://user-images.githubusercontent.com/30929671/203714843-2506d04c-9ec7-4143-9ff9-dbfe8aa82753.png)

- pid와 로지컬 어드레스를 페이지 테이블에 기록하거나 검색한다. i번째 엔트리에 해당 페이지가 있으면 i번째 프레임에 페이지 내용이 있다.
- 일치하는 값을 찾을 때까지 전체 테이블을 서치해야한다.
    - 기존 페이징 기법은 p만큼 jump 이는 TLB 캐시와 입장이 유사한데, 페이지 테이블은 주기억장치상의 자료구조라서 HW support를 받을 수 없음.
- pid와 p를 해시해서 검색 속도를 제한할 수 있고, 역시 TLB를 사용하면 서치할 필요도 없다
- 페이지 쉐어링이 불가능하다.

## 4. Segmentation

- page는 단위를 크기로 나누기 때문에 다음과 같은 문제점이 있음
    - 한 페이지에서 공유 가능한 내용과 그렇지 않은 내용이 섞이는 문제
    - 하나의 단위가 여러개의 페이지에 걸쳐있는 걸쳐있는 문제
- segmentation은 유저 관점의 메모리 관리 scheme을 제공한다.
    - segment는 stack, 배열, symbol table, main, function 등의 논리적 단위
    - 프로그램은 가변길이 세그먼트의 collection이다.

![image](https://user-images.githubusercontent.com/30929671/203714916-16138822-7228-4832-a013-e944589b1369.png)

- segment 테이블 존재, segment 단위로 효율적 공유 가능
- contiguous allocation의 문제가 다시 발생 → external fragmention
    
    (참고)
    
    페이지 : internal fragmention
    
    세그먼트: external fragmentation
    

### segmentation with paging

- logical address를 segment별로 자르고, segment를 page로 자른다.
    - segment는 더이상 contiguous 하지 않음
    - external framentation이 사라지고 internal fragmentation이 다시 발생
- 세그먼트 테이블이 존재하고, 세그먼트마다 페이지 테이블이 존재한다.

⇒ external fragmentation이 없어서 메모리 효율성은 paging과 같고, segment의 특징을 유지해서 효율적인 공유가 가능함. 

![image](https://user-images.githubusercontent.com/30929671/203714960-0814df84-271a-41af-bd2c-97f6094ad923.png)

### 더 공부할 내용

페이지 폴트 처리

페이지 교체 알고리즘

Thrashing
