### 모듈 `mysql2`를 쓰는 이유

기능 자체는 모듈 `mysql`과 동일하나 mysql은 async/await 적용이 불가하다고 한다. 따라서 async/await을 사용하기 위해서는 mysql2를 설치하여야 하고, 사용할 땐 `from mysql2/promise`로 한다.
<br>

### transaction

트랜잭션 하나하나 찍어봤는데 3의 과정이 없으면 쿼리문이 정상적으로 실행되지 않았다. 이유는 모르겠지만 아무튼 `await callback(connection)` 이 있어야 `executeQuery(queryString)`이 실행된다. 이게 의아한 건 변수에 담아서 콘솔로 확인해보면 `undefined`가 나오기 때문,, 머슥<br>

롤백이 잘 되나 보기 위해서 쿼리문의 컬럼명을 바꾸어 실행하였더니 정상적으로 error가 콘솔에 찍혔지만 res로는 넘어가지 않아서 의아했는데.. 내가 `throw error`를 하지 않아서였다. 요거 쓰면서 추가해줬더니 잘 된당. (ㅎㅎ)

```javascript
export async function transaction(callback) {
  let connection = null;

  try {
    // Error: Too many connections 시 transaction 시작하자마자 null return
    // 1. Pool에서 Connection 가져오기
    const connection = await pool.getConnection();

    // 2. 가져온 Connection에서 트랜잭션
    await connection.beginTransaction();

    // 3. ????? 이게 없으면 쿼리문 실행이 안 됨(콘솔로 확인 시 undefined)
    await callback(connection);

    // 4. 트랜잭션 반영
    await connection.commit();
  } catch (error) {
    if (connection) {
      // 4-1. 문제 발생 시 롤백
      await connection.rollback();
    }
    console.error("롤백에러", error);
    throw error; // res 값으로 에러 전달
    // return null;
  } finally {
    if (connection) {
      // 5. 트랜잭션 해제
      connection.releaseConnection(connection);
    }
  }
}
```
