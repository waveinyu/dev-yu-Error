들어가기 앞서서 이 에러는 라이트세일이라는 특수환경(?!) 때문에 완벽히 해결은 하지 못했다. 그치만 단독 서버를 사용할 미래의 나를 위해서 정리,, (그래서 솔루션이 없다.)

<br>

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

위의 에러를 해결하기 위해서 열심히 구글링을 해보니, 가상 서버의 데이터베이스에 슈퍼 권한을 주기 위해서는 직접 **데이터베이스의 환경 변수의 값을 변경**해주어야 한다는 걸 찾았다. **`log_bin_trust_function_creators`의 값이 false로 되어있는 것을 true로 변경**해야 한다는 것.

<br>

근데 저는 가상 서버인데 어떡해요,,, RDS는 친히 환경 변수를 설정하는 페이지가 따로 있어서 쉽게 할 수 있는데, 라이트세일은 직접 `aws_cli`을 설치해 직접 터미널로 확인해야 했다.<br>

- [Increase MySQL max_connection on Lightsail (not RDS) database](https://stackoverflow.com/questions/58132644/increase-mysql-max-connection-on-lightsail-not-rds-database) <br>
- [Getting started with the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)<br>
- [Updating database parameters in Amazon Lightsail
  ](https://lightsail.aws.amazon.com/ls/docs/ko_kr/articles/amazon-lightsail-updating-database-parameters)

  <br>

```javascript
// aws-cli lightsail parameters
aws lightsail get-relational-database-parameters --relational-database-name 데이터베이스이름
```

<img src="https://user-images.githubusercontent.com/99732695/212461213-95543d8f-53a7-4863-aa99-9ead42136e5f.png"> <br>

근데 조회를 해보니 이미 parameterValue가 1인 것,, 이날 나는 지인들에게 이길 뻔 하다가 비겼다고 했다. 왜냐면 시간 문제일 거라고만 생각했었고든,,

<br>

그래서 이번엔 우회해서 워크벤치에서 바로 권한을 부여하는 방식으로 갔는데

```sql
grant all privileges on `dbName`. * to `dbUser`@`endPoint` with grant option;
-- Error Code: 1410. You are not allowed to create a user with GRANT	0.031 sec
```

저 유저로는 권한 부여 자체가 허용이 안 된다고 해서 띠용..

<br>

그래서 해당 dbUser로 계정을 다시 만들었다. (성공)

```sql
create user 'dbUser'@'endPoint' identified by 'password';
```

<br>
그런 후에 다시 2번과 같이 권한을 부여하니 성공했다.

```sql
grant all privileges on `dbName`. * to `dbUser`@`endPoint` with grant option
-- 0 row(s) affected	0.000 sec
```

<br>
그래서 이번엔 되겠지 하고 다시 set global max_connections 진행하니 역시 또 똑같은 에러가 떴다.

```sql
SET GLOBAL MAX_CONNECTIONS=500
--Error Code: 1227. Access denied; you need (at least one of) the SUPER or SYSTEM_VARIABLES_ADMIN privilege(s) for this operation
```

우째서,,,,,<br>
이 이후로는 엄청 찾고 명령어 반복하다가 마지막으로 기억 나는 권한 삭제 명령어를 입력한 후 내가 접속하고 있었던 MySQL 유저가 아닌 다른 유저(회사 컴퓨터)로 접속이 되어 있었고 결과적으론 내가 작업하던 DB 스키마도 날아갔다. 날아가기 전에 현재 접속 유저와 데이터베이스를 조회했을 땐 있었던 테이블이 날아가서 얼마나 당황을 했던지,,<br>
워크벤치로는 루트 계정에 접속을 할수가 없고, 터미널은 로컬 서버만 접근할 수 있기 때문에 접근 자체를 못 하고 결국 새 데이터베이스 서버를 열어서 스키마를 다시 만들기로 했다. 그게 더 빠를 것 같더라구 ( ^^ )<br>

<br>

## _Lightsail_

근데 알고 보니 Lightsail은 독립서버가 아닌 웹 호스팅의 개념이라서, **max_connections**의 개수가 정해져있단다. 내가 조회해봤을 때 61개가 전부였는데 그게 디폴트 값에다가 변경이 안 된다고 한다.<br>
프로젝트 규모가 작아서 얼마나 다행인지,,, ( ^^ ) 중간에 DB 날아갔다고 생각했던 건 mySQL인 것 같아서 다행이었다. 나는 진짜 회사 뭐 중요한 데이터면 어쩌나하고 정말 중간엔 울 뻔 했다.

<br>

그래도 좀 재밌었다. 문제가 문제를 가져올 땐 머리 아팠지만 해결방법을 찾아가는 건 재밌었다. 나중에 독립서버를 운영할 땐 좀 덜 고생하겠지,,, 암튼 인상적인 날이라 기록! <br>

참고 : 별 거 아닌데 유용한 명령어

```sql
SELECT USER(); -- 1. 현재 사용자 확인

SELECT DATABASE(); -- 2. 현재 데이터베이스 확인

SHOW TABLES; -- 3. 테이블 확인
```
