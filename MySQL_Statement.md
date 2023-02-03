시행착오 겪을 때마다 갱신

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

테이블1, 테이블2의 정보를 한번에 조회하고 싶을 때

```sql
-- 1) 전체 조회(별칭 사용 가능)
SELECT *
FROM 테이블1 AS a
INNER JOIN 테이블2 AS b
ON a.조건컬럼 = b.조건걸럼

-- 2) 일부 컬럼 조회(별칭 사용 불가)
SELECT 테이블1.컬럼1, 테이블2.컬럼2  ...
FROM 테이블1
INNER JOIN 테이블2
ON 테이블1.조건컬럼a = 테이블2.조건걸럼b
```

컬럼1과 컬럼2의 이름은 꼭 같지 않아도 된다. (시중에 다른 inner join 문도 써봤는데 왜인지 이것만 적용됨)
