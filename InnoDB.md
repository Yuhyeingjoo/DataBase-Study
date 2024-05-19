# InnoDB 



![image](https://github.com/Yuhyeingjoo/DataBase-Study/assets/54518241/b59c1e36-e479-4109-9fe1-5cc37523a2a8)


## In Memory Structure


구성요소는 다음과 같다.:
1. Buffer Pool
2. Change Buffer
3. Adaptive Hash Index


---

1. Buffer Pool
   테이블 및 인덱스 데이터 캐싱을 목적으로 하는 메인 메모리 영역. 데이터 페이지, 인덱스 페이지, Undo 페이지 등 다양한 페이지가 캐싱된다. 자주 사용하는 메모리에 직접 접근하여(not 디스크 접근) 속도가 빠르며 그래서 buffer pool의 크기는 성능에 중요하다. 데이터베이스 용도로 쓰이는 서버는 버퍼 풀에 물리 메모리의 80%을 할당하기도 한다. 버퍼 풀은 페이지단위로 링크드리스트로 구성된다. 메모리에서 접근된 페이지는 리스트 앞단에 놓이며 그렇게 페이지가 쌓이다가 버퍼 풀 사이즈를 넘긴 뒷단의 리스트의 페이지는 삭제된다.
2. Change Buffer (Insert Buffer)
   Buffer Pool에 포함되는 영역으로 Secondary Index Page의 변경 내용을 캐싱하기 위한 임시 공간. Insert, Delete, Update는 많은 디스크 리소스가 필요함. Change Buffer에 임시 저장했다가 향후 다른 Read 명령에 의해 해당 페이지가 버퍼 풀에 로드될 때 병합된다. 그래서 디스크 IO를 줄일 수 있다.
3. Adaptive hash Index
   자주 사용되는 컬럼을 해시로 정의하여 B-Tree Index를 이용하지 않고 직접 데이터에 접근한다. 해시는 인덱스 키 값과 데이터 페이지 주소로 관리된다.
데이터베이스가 조회하려는 인덱스 키 값을 해시 함수에 입력으로 전달하여 해시 값을 생성한다. Adaptive Hash Index는 이 해시 값을 사용하여 해시 테이블에서 해당하는 데이터 페이지에 대한 주소(예: 페이지 번호)를 찾아서 바로 접근한다.
4. Log Buffer     
   Redo Log 파일을 기록하는 메모리 영역. InnoDB는 Log Buffer에 데이터 변경사항을 저장해둔 후에 설정된 주기 혹은 트랙잭션 커밋 시점에 디스크로 쓴다.


## On Disk Sructure

InnoDB는 모든 데이터를 디스크 상의 Tablespace라는 논리적인 공간에 저장한다. 

1. TableSpace
   데이터를 저장하는데 사용되는 가장 큰 논리적인 단위이며, 내부적으로 Segment -> Extent -> Page -> Row로 구성된다.
MySql에서 기본적으로 사용하는 것은 system Tablespace이다. Change Buffer의 저장 영역이며 테이블 및 인덱스 데이터 저장 영역.
2. Index
   MySql의 인덱스의 대부분은 B-Tree 형태다.
InnoDB 테이블에는 Clustered Index(primary Key) 라는 특별한 인덱스가 존재한다. 클러스터 인덱스는 PK값을 이용하여 Data를 정렬하여 저장합니다. EID(Key)값과 포인터가 한 쌍으로 노드에 있으며, 트리 구조를 이룬다. 포인터를 통해 해당 페이지로 바로 접근 가능하다. 기본 키를 기반으로 한 검색이나 정렬은 클러스터 인덱스를 따라 물리적인 테이블의 순서를 따라가므로, 검색 및 정렬이 매우 효율적으로 수행됩니다. 
Secondary Index
기본 키가 아닌 열의 값으로부터 기본 키를 찾게 해주는 인덱스. 열의 값과 그 레코드의 기본키를 매핑한다. 그래서 세컨더리 인덱스로 값을 검색하면 먼저 기본 키를 찾고 기본 키로부터 해당 테이블을 찾는다. 
3. Double Write Buffer
   InnoDB는 Data 손실을 방지하기 위해 DoubleWrite 라는 파일 Flush 기능을 사용한다. Buffer Pool로부터 Flush 된 page를 Data 파일에 쓰기 전에 저장하는 영역으로 디스크상에 파일로 존재한다. 

1.	Buffer Pool의 페이지가 한 번에 flush 된다.
2.	여러 페이지가 double write 버퍼에 write 한다. 
3.	Double write 버퍼에 쓰인 페이지들을 순차적으로 버퍼 풀에서 디스크로 쓴다. 
