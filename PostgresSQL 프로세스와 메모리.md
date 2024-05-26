# 프로세스

PostgreSQL은 postmaster와 postgres 프로세스를 통해 서버가 동작함. 
![image](https://github.com/Yuhyeingjoo/DataBase-Study/assets/54518241/0abb65ee-449e-4432-94d9-20b551bb5ff8)

## Postmaster
- PostgreSQL 서버를 가동/중지하는 메인 프로세스로 가장 먼저 시작되는 프로세스.
- Postmaster는 Shared Memory를 사용하며 다양한 백그라운드 프로세스를 할당함.
- 클라이언트 연결 요청을 대기하며, 연결 요청이 있으면 postgres 프로세스를 생성함.
- 가장 상위 프로세스로 하위 프로세스의 비정상 작동유무를 체크함.

## Postgres(백엔드 프로세스)
  - 클라이언트 요청 처리하는 프로세스로, 연결이 유지되는 동안 실행됨.
  - Postmaster에 의해 실행되며 클라이언트에 1대1.
  - conf 파일에서 max_connections가 최대 백엔드 프로세스 제한.

# PostgreSQL의 메모리 구조

## 메모리 구성
- PostgreSQL은 서버 시작 시 공용 메모리 공간인 Shared Memory와 백엔드 프로세스를 위한 Local Memory를 할당한다.
- Shared Memory는 데이터 블록 및 트랜잭션 로그 등의 정보를 캐싱하는 메모리로 모든 프로세스에 공유된다.
- Local Memory는 백엔드 프로세스 별로 할당되어 공유 불가능하며 정렬 등 프로세스 로직 처리를 위한 메모리 공간이다.


![image](https://github.com/Yuhyeingjoo/DataBase-Study/assets/54518241/8a06a1a0-28d0-455a-a123-1ddcde183658)


## Shared Memory

1. Shared Buffer
   - 데이터와 데이터 변경사항을 블록 단위로 캐싱하여 I/O를 빠르게 처리하기 위한 메모리
   - postgresql.conf의 shard_buffers 파라미터 값으로 크기를 설정한다(Default 128MB).
   - Shared Buffer에 기록되는 단위는 블록 사이즈인 8K 단위이다.

2. Clog Bffer
   - 각 트랜잭션의 상태 정보를 캐싱하는 공간으로 모든 트랜잭션의 상태가 있으며 완료 여부를 확인할 수있도록 하는 영역.

3. Lock Space
   - Shared Memory 영역 중 Lock과 관련한 데이터를 보관하는 영역으로 사용되는 모든 Lock 정보를 저장함.
   - 사용자가 특정 테이블에 접근하는 경우 특정 트랜잭션이 해당 테이블을 삭제 또는 수정하지 않도록 활동을 추적해야 함. 이 활동에 대한 정보를 Lock Spqce 에 저장함.


## Local Memory

1. Maintenance Work memory
   - 유지 관리 작업에 사용되는 메모리로 기본 64MB.
   - 인덱스 생성, 테이블 변경, Forign Key 추가 등의 작업에 사용되는 공간.
2. Temp Buffer
   - Temporary 테이블에 사용되는 공간으로 temp_buffers는 기본 8MB.
   - Temp 테이블을 사용하는 경우에만 할당되는 공간.
3. Work Memory
   - 과도한 정렬/Hash 작업이 발생하여 Temp 파일을 사용하기 전에 사용되는 공간으로 기본 4MB.
   
