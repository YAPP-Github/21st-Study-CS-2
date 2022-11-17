# 페이지와 세그먼테이션 (1)

# background

1. 프로그램은 메모리에 올라와서, 프로세스에 배치되어야 실행 가능
2. CPU는 메모리, 레지스터, 캐시에만 접근 가능
3. 프로세스는 스스로의 주소 영역에만 접근해야함 (다른 프로세스, 커널 ㄴㄴ)

1. 바인딩
    - 컴파일 타임 바인딩: 컴파일러에 의해 주소가 결합
        - 싱글 프로세스에서만 작동
        - OS-dependent
    - 로드 타임 바인딩: loader에 의해 주소가 결합
        - 멀티 프로세스 환경에서 작동
        - OS-independent
        - 프로세스의 위치 이동 불가
    - Execution 타임 바인딩: cpu가 상대 주소를 발행하면, 매핑 테이블을 참고해서 주소를 찾음
        - 프로세스가 메모리 상에서 위치 이동 가능
        - 상대 주소에 일종의 offset을 더해주는 과정을 위한 하드웨어 지원 필요
            - MMU (mem management unit)
            - base register + logical address
            

![Untitled](%E1%84%91%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%8C%E1%85%B5%E1%84%8B%E1%85%AA%20%E1%84%89%E1%85%A6%E1%84%80%E1%85%B3%E1%84%86%E1%85%A5%E1%86%AB%E1%84%90%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20(1)%200004003480514e1c9268ffeea3d6b6d4/Untitled.png)

1. Logical addr space vs Physical addr space
    - Logical Address Space
        - cpu가 접근하기 위해 발생시키는 주소체계
        - = virtual address
    - Physical Address Space
        - 실제 DRAM이 갖는 주소체계
    - 분리 이유: execution time binding 하기 위해서
        - 로드타임, 컴파일타임 바인딩에서는 구분이 없음

### Execution time  binding

![Untitled](%E1%84%91%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%8C%E1%85%B5%E1%84%8B%E1%85%AA%20%E1%84%89%E1%85%A6%E1%84%80%E1%85%B3%E1%84%86%E1%85%A5%E1%86%AB%E1%84%90%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20(1)%200004003480514e1c9268ffeea3d6b6d4/Untitled%201.png)

*relocation register == 프로세스 시작 위치 

### 의문점

1. 덧셈 때문에 하드웨어를 둬야 하나?
    - 페이징 기법은 덧셈 한번으로 끝나지 않음
2. 프로세스의 주소 공간의 위치가 바뀌어야 하는 이유는?
    - OS의 mid term scheduler가 프로세스를 디스크로 swap-out 시켜야 하는데,  Swap - in 시에 원래 위치로 돌아올 수 있다는 보장이 없음

### contiguous Allocation

 한 프로세스를 메모리에 연속적으로 저장하는 것.

- contiguous allocation이어야 논리적 주소 + 프로세스 시작 주소 = 실제 주소가 성립함
- 문제점: 프로세스 swapping 과정에서 hole 발생 (Fragmentation)
    
    ![Untitled](%E1%84%91%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%8C%E1%85%B5%E1%84%8B%E1%85%AA%20%E1%84%89%E1%85%A6%E1%84%80%E1%85%B3%E1%84%86%E1%85%A5%E1%86%AB%E1%84%90%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20(1)%200004003480514e1c9268ffeea3d6b6d4/Untitled%202.png)
    
- allocation policies
    1.  first fit : 프로세스가 들어갈 수 있는 첫 공간
    2. best fit: 프로세스가 들어갈 수 있는 것 중 가장 작은 공간
    3. worst fit: 가장 큰 공간에 할당

### Fragmentation

fragment: 자투리공간

fragmentation: 자투리 공간을 만들어 내는 것

1. External Fragmentation
    
    할당이 안 되어서 사용이 가능한데, 너무 작아서 효용이 떨어지는 공간 
    
    - Compaction : fragment를 큰 블록으로 합치는 기법
    - 모든 주소공간의 base addr이 변화하고, 전체 메모리의 카피가 필요함 → 비효율적 →Uncontiguous allocation
2. Internal Fragmentation
    
    할당 받은 프로세스가 사용을 안하는 공간 
    
    ex) 1024 byte 할당 받고 1000byte만 사용
    
    개선의 여지가 없음
    

### Uncontiguous Allocation

External fragmentation 문제를 해결하기 위해, 프로세스를 레고 블록처럼  나눈 뒤 빈 공간에 할당 

구체적으로, 피지컬 메모리를 같은 크기의 블록으로 자른다 (프레임)

유저 프로세스를 프레임과 같은 블록으로 분할한다 (페이지)

![Untitled](%E1%84%91%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%8C%E1%85%B5%E1%84%8B%E1%85%AA%20%E1%84%89%E1%85%A6%E1%84%80%E1%85%B3%E1%84%86%E1%85%A5%E1%86%AB%E1%84%90%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20(1)%200004003480514e1c9268ffeea3d6b6d4/Untitled%203.png)

- 장점 : external fragmentation 원천적 차단
    - internal fragmentation은 마지막 페이지에만 생김
- 문제점
    - 프로세스의 시작 위치를 몰라서, 각 페이지의 저장 위치를 관리하는 자료구조 필요 **(페이지 테이블)**
        - 주소 변환 과정이 복잡해진다

### Paging: Address Translation Architecture

![Untitled](%E1%84%91%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%8C%E1%85%B5%E1%84%8B%E1%85%AA%20%E1%84%89%E1%85%A6%E1%84%80%E1%85%B3%E1%84%86%E1%85%A5%E1%86%AB%E1%84%90%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%A7%E1%86%AB%20(1)%200004003480514e1c9268ffeea3d6b6d4/Untitled%204.png)

p: page number

d: page offset 

f: frame 주소 

**MMU가 하는 일**

1. CPU가 발행한 주소를 page number와 offset으로 나눈다
2. page table lookup
3. frame 주소 + page offset

⇒ 별도의 하드웨어 필요

### paging: problems

1. 페이지 테이블 자료구조 필요
2. **페이지 테이블을 참조해야함 → 메인 메모리에 올라와 있어야함**
    - 프레임의 내용을 찾으려면 메인 메모리에 2번 접근해야함
    - **TLB cache (translation lookup-buffer)**
3. 페이지 테이블의 위치를 참조하기 위한 레지스터 필요 (PTBR)
4. 페이지 테이블의길이를 알기위한 레지스터 필요 (PTLR)

### TLB 캐시의 요구사항

1. 빠르게 동작해야함
    - 모든 record를 parerrel search 할 수 있는  Associative memory를 이용하여 해결
2. cache miss rate가 낮아야함
    - locality가 괜찮음.

### paging + tlb

contiguous allocation 에 비해 성능을 약간만 희생해서 external fragmentation 해결할 수 있다.

페이지테이블에 valid bit을 설정해서 page를 쉐어할 수 있음

### next

1. memory protection, shared pages
2. hierachical, hashed, inverted page table 
3. segmentation, segmentation with paging