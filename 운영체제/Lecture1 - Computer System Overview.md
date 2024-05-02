### Basic Elements
- processor
- Main Memory
	- 휘발성
- I/O modules
	- secondary memory devices (disks) - 하드 디스크
	- communications equipment
	- terminals
- System bus
	- 프로세서, 메모리, 입출력장치와 소통

### Computer Component : Top-Level View
- 프로그램 실행 순서
	1. 초기 I/O 내부 하드디스크에 위치
	2. Main Memory 이동
	3. 한 줄 씩 CPU 이동
- I/O 내부에 버퍼가 있는 이유
	- 속도 향상 (I/O 초단위 / CPU ns단위)
- PC - 다음에 실행할 **것 같은** 명렁의 주소 저장
- MAR, MBR - System Bus와 직접 연결

### Instructions
- 프로그램 : 명령어와 데이터의 집합
- 명령어
	- Processor-Memory data transfer
	- Processor-I/O data transfer
	- Data processing
	- Control

### Instruction Execution
- Two steps
	- 메모리로부터 프로세서가 명령어를 읽어옴
	- 프로세서가 각 명령어를 실행
- Basic Instruction Cycle
	- start -> fetch next instruction -> execute instruction -> halt
		- fetch  stage - 명령을 가져옴
		- execute stage - 명령을 분석, 데이터를 가져와야할 경우 가져옴

### Control and Status Registers
- Program Counter (PC)
	- 가져올 명령어의 주소 포함
- Instruction Register (IR)
	- 가장 최근에 가져온 명령어의 주소 포함
- Program Status Word (PSW) : 16비트 명령어들의 비트를 나누었을 때 각각의 뜻
	- Condition code : 0보다 큰지, 작은지, 같은지, overflow인지 4가지 검사
	- Interrupt enable / disable : 인터럽트 여부 표시 (1bit)
	- Supervisor / user mode : OS / 프로그램 작업에 따른 권환 (1bit)
	*3가지를 제외한 나머지 register는 범용 register (general, 값 저장) (덜 중요)*

### Example of Program
![[Pasted image 20240419002151.png]]
- 싸이클 사이에서 interrupt 발생 시 PC, AC 값 등이 초기화 될 수 있음
	--> CPU 내 모든 register의 내용을 메모리에 저장, interrupt 후 restore
	*Step 1 : fetch 끝난 후 이미지*
	*Step 4 : 3 + 2 = 5 에서 2는 MBR에 존재*\
- #### Fetch Stage
	- 첫 번째 fetch stage 시작 전 
		- PC : 300
		- IR : 이전 실행 명령
	1. MAR <- PC, PC++ (사실상 자동으로 ++)
	2. memory read, MBR <- (MAR에 저장된 번지의 명령)
	3. IR <- MBR
- #### Execution Stage #1
	1. 명령 분석 ***1**940 -> **1**  : 가져오기*
	2. MAR <- IR의 주소 부분 *1**940** ->**940***
	3. memory read, MBR <- (MAR에 저장된 번지의 데이터) *940 -> |0003|*
	4. AC <- AC + MBR
		
- #### Execution Stage #2
	1. 명령 분석 ***5**941 -> **5**  : 더하기*
	2.  MAR <- IR의 주소 부분 *5**941** ->**941***
	3. memory read, MBR <- (MAR에 저장된 번지의 데이터) *941 -> |0002|*
	4. AC <- AC + MBR

- #### Execution Stage #3
	1. 명령 분석 ***2**941 -> **2**  : 저장*
	2.  MAR <- IR의 주소 부분 *2**941** ->**941***
	3. memory write,(MAR에 저장된 번지의 memory 공간) <- MBR *941 <- |0005|*

### Interrupts
- 정상적인 프로그램의 실행을 중단시키는 이벤트
- Classes of Interrupts
	- Program : 발생한 원인이 나에게 있음
		- overflow
		- devision by zero
		- illegal machin instruction
			- jump 오류 (data를 instruction으로 읽은 경우)
			- hard-disk에서 메모리로 올라올 때 오류
		- reference outside allowed memory space
		- *4가지 모두 hardware interrupt*
	- Timer
	- I/O : *I/O 작업을 하기 위한 것이 아니라, 작업이 끝남을 알리기 위한 것*
	- Hardware failure
- 대부분의 I/O 장치는 프로세서보다 느리기에 I/O 인터럽트 사용

### Program Flow Control
![[Pasted image 20240419141944.png]]
- (b)의 4번은 I/O작업을 하기 위한 전처리 과정
- (b)의 Interrupt Handler는 I/O작업이 끝났으니 5번을 실행하라고 interrupt를 발생시킴
- (b)의 5번은 I/O작업이 잘 끝났는 지 확인

![[Pasted image 20240419141957.png]]
- (c) 실행순서
	- 1 -> 4 -> 대기 -> 5 -> 4 -> 3 -> 5

### Interrupt Handler
- Interrupt 종류가 많기에 가장 먼저 발생한 이유(hardware가 보관 중) 파악

### Interrupt Cycle with Interrupts
![[Pasted image 20240419144653.png]]
- fetch - execute 단계는 clock에 맞는 hardware로 작동하기에 그 사이에 interrupt가 발생한다 해도 중단되지 않음 -> 끝나고 실행
- Interrupt Stage에서는 interrupt 종류 확인 후 맞는 interrupt handler 

### Simple Interrupt Processing
- Hardware : interrupt 시작 전 hardware적으로 해야하는 준비
	1. 인터럽트 요청
	2. 프로그램 실행 중단
		- fetch - execution 먼저 마무리
	3. 인터럽트 확인 신호 보냄
	4. PC와 PSW 먼저 저장
		- #### 왜 이 작업을 hardware적으로 하는가?
			-->  
	1. 인터럽트의 새로운 PC값 불러옴
		- 여기서 인터럽트 시작 주소를 PC에 넣기에 바로 인터럽트 프로그램 실행하며 software적으로 넘어감
- Software
	6. 남은 프로세스 상태들 저장
	7. 인터럽트 실행
	8. 프로세스 상태 정보 복구
	9. PC와 PSW 복구
		- 마찬가지로 PC를 바꾸는 순간 프로그램이 바뀌기에 마지막에 위치