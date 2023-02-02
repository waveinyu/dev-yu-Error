### 1. 현재 날짜/시간 디폴트값 주기

```sql
-- 테이블 생성 시 디폴트로 현재 날짜시간 설정
create table renewal.events(writeDate DATETIME DEFAULT CURRENT_TIMESTAMP);
create table renewal.events(writeDate DATE DEFAULT (CURRENT_DATE));

-- 이미 있는 컬럼 디폴트값을 현재 날짜시간으로 수정
ALTER TABLE `renewal`.`events`
MODIFY COLUMN writeDate DATE NOT NULL DEFAULT (CURRENT_DATE);
```
