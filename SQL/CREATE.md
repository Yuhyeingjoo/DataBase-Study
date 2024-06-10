# CREATE

테이블 'cars'를 생성하는 SQL

```sql
CREATE TABLE cars(
  brand VARCHAR(255),
  model VARCHAR(255),
  year INT
);
```

brand, model, year 컬럼의 cars 테이블을 생성한다. 
쿼리를 실행한 결과 DB에 cars 테이블이 추가된 것을 확인했다. 

![image](https://github.com/Yuhyeingjoo/DataBase-Study/assets/54518241/0d0110d6-6a35-452d-b9eb-f4949c1556ec)


```sql
select * from cars;
```

이제 방금 생성한 테이블의 모든 row를 읽는 SELECT 명령을 실행한다. 
Result를 보면 아직 아무런 row가 없음을 확인했다.
![image](https://github.com/Yuhyeingjoo/DataBase-Study/assets/54518241/af187857-2432-4af0-897b-f887d0492277)


그러면, 해당 테이블에 INSERT로 데이터를 추가하겠다.

```sql
INSERT INTO cars (brand, model, year)
VALUES ('Ford', 'Mustang', 1964);
```


먼저 INSERT 쿼리를 실행한 후,
![image](https://github.com/Yuhyeingjoo/DataBase-Study/assets/54518241/5b688db5-4101-4a43-80e6-6b5693ac24c0)


SELECT 쿼리로 모든 row를 읽어온다. 
방금 추가한 데이터를 읽어옴을 확인했다.
![image](https://github.com/Yuhyeingjoo/DataBase-Study/assets/54518241/5dd8f4f5-1fa8-462e-bf71-e5d0345bff89)



그러면 한 SQL 쿼리로 여러 데이터를 추가해보자.

```sql
INSERT INTO cars (brand, model, year)
VALUES
  ('Volvo', 'p1800', 1968),
  ('BMW', 'M1', 1978),
```
먼저 INSERT로 데이터를 추가하고,
![image](https://github.com/Yuhyeingjoo/DataBase-Study/assets/54518241/2a2c134a-39b2-462f-84d1-aab6091e9009)

올바르게 추가됐는지 확인한다.
![image](https://github.com/Yuhyeingjoo/DataBase-Study/assets/54518241/1cb97b2a-da65-444b-93bb-e224a52de6ab)


(INSERT 쿼리를 실행하고 성공적으로 데이터가 추가되었으면 쿼리는 "INSERT 0 [추가된 row 개수]" 를 반환하는데 직접 실습해보니 추후 반영이 필요해 보인다.)

