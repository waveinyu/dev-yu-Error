들어가기 앞서서 이 에러는 라이트세일이라는 특수환경(?!) 때문에 완벽히 해결은 하지 못했다. 그치만 단독 서버를 사용할 미래의 나를 위해서 정리,,

## _error_

태초에 Too many connections가 있었다 . . . <br>

```javascript
Error: ER_CON_COUNT_ERROR: Too many connections
```

이 에러가 뜨면 바로 500 에러를 내뱉는다. 로컬에서 작업하던 중 처음 이 에러를 발견했을 땐, 고작 로컬 작업에서 왜 Too many한지도 모르겠어서 구글링 하다가 돌아오면 또 서버가 잘 돼서 그냥 넘어갔었는데... 잠깐 생각해보니까 배포할 때도 똑같이 저 에러가 나면 결국 서버가 다운되는 거 아닌가,, 하는 생각이 들어서 **원인을 해결하려면 아예 저 에러를 처리를 해야겠다**란 생각으로 열심히 구글링을 했는데...

<br>

```sql
SHOW STATUS like 'MAX_USED_CONNECTIONS'; --사용한 최대 커넥션 수 확인

SHOW STATUS LIKE 'ABORTED%'; --실패한 커넥션 수 확인

SHOW VARIABLES LIKE '%MAX_CONNECTION%'; --최대 커넥션 수 확인

SHOW VARIABLES LIKE '%TIMEOUT%'; --지속 시간 확인 (default :  28800초, 총 8시간)

SET GLOBAL MAX_CONNECTIONS=500; --최대 커넥션 수 500으로 세팅
```

`Too many connections`에 대한 해결방법 자체는 어렵지 않게 찾았는데, 마지막 최대 커넥션 수 세팅하는 문장에서 또
에러가 발생했던 것이다 . . .

<br>

```sql
SET GLOBAL MAX_CONNECTIONS=500;
-- Error Code: 1227. Access denied; you need (at least one of) the SUPER or SYSTEM_VARIABLES_ADMIN privilege(s) for this operation
```

1. 라이트세일 이용
2. aws-cli
3. mysql 현재 사용자 조회, 현재 데이터베이스 조회, 테이블들 조회
