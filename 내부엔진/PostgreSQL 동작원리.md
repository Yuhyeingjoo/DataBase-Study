# 동작 원리

## 커넥션 생성
![image](https://github.com/Yuhyeingjoo/DataBase-Study/assets/54518241/99f6a97a-c4b4-4aee-b508-c63c4363d005)

1. postmaster 프로세스는 클라이언트의 새로운 연결 요청을 기다린다.
2. postmaster 프로세스는 클라이언트의 요청에 대해 인증하고 인증이 승인되면 postgres 프로세스를 생성한다.
3. postgres 프로세스는 work_mem 같은 Loacal Memory가 개별 할당한다.
4. postgres 프로세스가 클라이언트와 연결되면 postmaster 프로세스는 클라이언트와의 연결을 해제하며 postgres가 연결을 관리한다. 
   ![image](https://github.com/Yuhyeingjoo/DataBase-Study/assets/54518241/9a46d45f-870b-40e6-835e-0c434a594740)
5. postgres 프로세스는 쿼리 실행 및 결과 반환 처리한다.


## 클라이언트 쿼리 요청 과정

- postgres 프로세스는 데이터를 읽고 쓸 때 Database Buffer 또는 RAM을 사용한다. 사용할 메모리 사이즈는 postgresql.conf 파일의 shared_buffer 값에 의해 결정되며 서버 시작 시 해당 크기 만큼 할당된다.
- 사용자 요청은 읽기(Select)와 변경(Insert, Delete, Update)으로 구분한다. 하기 단계 과정을 거친다.


1. postgres 프로세스는 shared buffer에서 원하는 데이터 블록을 사용할 수 있는지 확인한다.
2. shared buffer를 사용할 수 없는 경우 OS로 요청하여 OS에서 처리한다. OS 캐시에 해당 블록이 존한다면 해당 블록을 전달할 수 있다.
3. OS 캐시에도 해당 데이터가 없는 경우 디스크에서 요청된 블록을 shared buffer로 캐싱하며 이 경우 IO가 발생한다.
4. 변경 작업인 경우 변경은 shared buffer 안에서 이루어지며 변경 Data(Dirty data) 및 변경 로그는 향후 일정 조건에 의해 Data 파일 및 WAL 파일 동기화 됩니다.


## 변경 데이터의 저장

변경 요청된 데이터는 Shared buffer에 저장된다. 캐시 상에 있는 메모리는 Disk에 반영돼야 하는데 BG Writer와 checkpointer가 그 역할을 한다.
Dirty Data 상태의 블록을 디스크에 쓰는 동기화 작업을 수행하며 하기 조건일 경우 동기화 작업이 시작된다.
1. BG Writer는 정기적으로 Dirty 블록을 디스크에 쓴다.
2. Checkpointer는 체크포인트 발생 시 Dirty 블록을 디스크에 쓴다.
     - 이전 체크포인트 발생 후 5분 경과
     - WAL 파일 크기가 max size를 초과
     - super 유저에 의해 체크포인트 커맨드 실행
     - pg_baebackup으로 백업을 시작하는 경우
