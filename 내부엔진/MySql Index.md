# MySql 인덱스란

DB의 데이터를 조회할 때 타겟 데이터를 빠르게 검색하기 위한 데이터베이스 개체. 인덱스는 빠른 검색을 통해 성능에 도움을 주지만 인덱스를 위한 저장 공간이 필요하고 데이터를 자주 변경할 시 인덱스도 그에 따라 수정되어야 하기 때문에 경우에 따라 성능 저하를 일으킬 수 있다.

### 인덱스의 특징
1. 장점
   - 인덱스를 통한 조회 성능 개선 가능
2. 단점
   - 인덱스를 위한 데이터 공간이 전체 DB의 약 10%를 차지함.
   - 조회(select)가 아닌 DB 변경 작업 시 오히려 성능 저하 발생할 수 있음.


---



### 인덱스 종류

1. 클러스터 인덱스
   - 테이블 당 1개 존재함.
   - 기본 키로 설정된 컬럼은 자동으로 클러스터링 인덱스로 생성됨.
   - 클러스터링 인덱스를 기준으로 정렬됨
   - 실제 데이터와 같은 페이지 구조를 가짐.
   - 
2. 보조 인덱스 (Secondary Index)
   - 테이블에 여러 개 설정 가능함.
   - UNIQUE 키워드로 고유 컬럼 지정 시 자동으로 보조 인덱스 생성됨.
   - 실제 저장된 데이터와 다른 별도의 페이지 구조를 가짐.
   - 인덱스로 정렬하지 않음.
   - CREATE INDEX로 직접 보조 인덱스 생성 가능함.
  


## 인덱스 동작 원리

클러스터 인덱스와 보조 인덱스 모두 B-Tree로 만들어진다. B-Tree 구조에서 데이터 저장되는 공간을 '노드'라고 한다. 

노드는 루트노드, 중간노드, 리프노드로 구성된다. MySql에서 이러한 노드를 페이지라고 부른다.

- 루트 페이지에서 검색할 데이터가 몇 번 페이지에 있는지 탐색한다.
- 리프 노드에 있는 타겟 데이터에 접근한다.
- 루트 노드에서 리프 노드에 접근하면서 몇 개의 페이지를 거쳐갔는지로 효율성을 판단한다.


인덱스의 저장 방식을 이해하기 위해서는 페이지에 대해 알아야 한다. 페이지란 디스크와 메모리에 데이터를 읽고 쓰는 최소 작업 단위다. 
페이지는 인덱스 키를 바탕으로 정렬된 상태다. 
![image](https://github.com/Yuhyeingjoo/DataBase-Study/assets/54518241/199fa05e-898d-459c-93e9-5c2871c110a3)


```sql
CREATE TABLE employee (
    emp_no INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(64),
    PRIMARY KEY(emp_no),
   
) ENGINE=InnoDB;
```
![image](https://github.com/Yuhyeingjoo/DataBase-Study/assets/54518241/a6dfcdda-4091-4587-a663-42877724287c)

원하는 데이터를 접근할 때 페이지 번호를 향해 리프 노드로 내려 가는 과정이 O(n)이 소요도지 않고 O(logN)이 소요된다.


