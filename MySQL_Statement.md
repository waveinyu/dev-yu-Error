### 1. 현재 날짜/시간 디폴트값 주기

```sql
-- 테이블 생성 시 디폴트로 현재 날짜시간 설정
create table renewal.events(writeDate DATETIME DEFAULT CURRENT_TIMESTAMP);
create table renewal.events(writeDate DATE DEFAULT (CURRENT_DATE));

-- 이미 있는 컬럼 디폴트값을 현재 날짜시간으로 수정
ALTER TABLE `renewal`.`events`
MODIFY COLUMN writeDate DATE NOT NULL DEFAULT (CURRENT_DATE);
```

### 2. inner join

```sql
SELECT *
FROM 테이블1 AS a
INNER JOIN 테이블2 AS b
ON a.컬럼1 = b.컬럼2
```

컬럼1과 컬럼2의 이름은 꼭 같지 않아도 된다. (시중에 다른 inner join 문도 써봤는데 왜인지 이것만 적용됨)
