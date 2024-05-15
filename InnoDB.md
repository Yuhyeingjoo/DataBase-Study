# InnoDB

MySql의 기본 스토리지 엔진이며 가장 보편적으로 많이 사용된다. 


![image](https://github.com/Yuhyeingjoo/MySql-Study/assets/54518241/b97823fe-99bf-46f9-9d0d-5a2125e49773)

InnoDB 엔진 사용 시 sql 작업은 ACID 모델을 따른다. 또한 Commit, Rollback 및 복구 기능을 갖춘 트랙잭션으로 데이터를 보호한다. 

- ACID란 
- Atomicity (원자성): 트랜잭션의 모든 작업은 성공 또는 실패로 전체적으로 수행되어야 합니다. 즉, 트랜잭션 내에서 하나의 작업이라도 실패하면 전체 작업이 롤백되어야 합니다.
- Consistency (일관성): 트랜잭션의 수행 전후에 데이터베이스는 일관된 상태여야 합니다. 즉, 모든 제약 조건이 만족되어야 하며, 데이터베이스의 무결성이 유지되어야 합니다.
- Isolation (고립성): 여러 트랜잭션이 동시에 실행될 때, 각 트랜잭션은 다른 트랜잭션의 작업에 영향을 받지 않아야 합니다. 즉, 한 트랜잭션이 실행 중인 데이터를 다른 트랜잭션이 변경할 수 없습니다.
- Durability (지속성): 트랜잭션이 성공적으로 완료되면 그 결과는 영구적으로 유지되어야 합니다. 즉, 시스템 장애가 발생하더라도 트랜잭션의 결과는 보존되어야 합니다.

InnoDB의 핵심은 Buffer Pool의 존재이다. 디스크 상의 데이터 파일이나 인덱스 정보를 메모리에 캐시해두기 위한 공간이다. 디스크의 IO 횟수를 줄일 수 있다. Primary Key를 기반으로 쿼리를 최적화하도록 디스크에 Data를 정렬하여 저장한다. Row-Level Lock 및 MVCC를 이용하여 멀티 유저에게 접근을 제공한다. 데이터 무결성을 보장하기 위해 Foreign Key를 사용한다. 

- Foreign Key는 한 테이블의 열(속성)이 다른 테이블의 기본 키(Primary Key)나 후보 키(Candidate Key)를 참조하는 제약 조건. 기본키를 참조하여 외래 키의 데이터 무결성을 보장할 수 있고 DBMS 경우에 따라 기본키와 외래 키가 동시에 업데이트 되도록 지원할 수 있다.



### Thread

MySql은 프로세스가 아닌 스레드 기반으로 동작한다. 스레드는 2가지로 구분되는데 유저 세션에 해당하는 Foreground thread와 내부 처리 목적으로 하는 Background Thread가 여기에 해당한다. 
보통 클라이언트 커넥션 당 하나의 스레드를 사용하여 명령문을 실행한다. 그래서 많은 클라이언트가 있을수록 서버의 성능이 저하된다. 그러나 최근 MySql 에디션에서는 스레드 풀을 이용하여 성능 저하를 최소화하는 버전도 릴리즈된다.

1. Foreground Thread
   해당 스레드는 서버에 접속한 클라이언트 수만큼 존재함. 각 클라이언트는 사용자가 요청한 쿼리를 처리한다. 사용자가 작업을 마치고 세션이 종료되면 해당 스레드는 스레드 캐시로 반환된다. 캐시에 들어갈 수 있는 스레드 수는 사용자 설정에 의해 제한되며 스레드 캐시로 인해 같은 클이언트가 서버에 다시 접속했을 때의 오버헤드를 줄인다. InnoDB 에서는 메모리에 대한 읽기/쓰기 작업만 해당 스레드가 담당한다.
2. Background Thread
   InnoDB는 많은 작업을 Background Thread가 수행한다. InnoDB 엔진의 주요 백그라운드 스레드는 다음과 같다: Master Thread, Insert Buffer Thread, Log thread, Read Thread, Writ thread, Page Cleaner thread, Purge thread
      - Master thread
         백그라운드 스레드 관리 및 다양한 태스크의 스케줄링 담당. Buffer pool의 데이터를 디스크로 비동기 Flush.
      - Insert Buffer thread
         Insert buffer를 Merge한다.
      - Read/Write Thread
         읽기/쓰기 요청을 처리한다

