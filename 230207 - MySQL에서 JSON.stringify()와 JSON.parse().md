## _error_

MySQL에서 객체를 저장할 때, 데이터 타입을 JSON으로 설정하여 그대로 저장하는 게 아니라 LONGTEXT로 설정 후 객체를 문자열로 바꾸어 저장하는 상황이었다. `JSON.stringify()`가 잘 됐는데 어쩐 일인지 쿼리를 짜면 에러를 뱉으면서 저장이 되질 않았다. 에러는 `ER_PARSE_ERROR 1064`. <br>

```sql
-- ...error

one": "사과",
  "two": "배"
}"
```

내가 작성한 쿼리가 이상한 건지 코드도 여러번 보고 콘솔도 여러번 찍었는데 이유는 모르겠고,, 그래도 여러번 출력결과를 확인해보니 문자열 문제라는 걸 깨닫게 됐다. 쿼리 에러에서 잘못됐다는 부분이 문장의 요상한 중간부터 출력되었기 때문...

<br>

```javascript
const obj = {
  one: "딸기",
  two: "바나나",
};
```

```json
-- obj
{ one: '딸기', two: '바나나' }

-- JSON.stringify(obj, null, 2)
{
  "one": "딸기",
  "two": "바나나"
}
```

<br>

## _solution_

결국 알아낸 건... `JSON.stringify()`를 하면 문자열로 되지만 출력을 할 땐 `""` 처리가 되지 않는다. 그래서 쿼리를 작성할 때 별도로 싱글코트로 감싸주어야 하는데, 이스케이프 문자(`\'`)를 사용해주어야 한다. 아래의 `\'${str}\'`이 아닌 `"${str}"` 이런 식으로 하게 되면 값은 저장되지만 파싱할 때 문제가 생기니 한 번 저장할 때 잘 해야 한다.

<br>

```javascript
const obj = {
  one: "딸기",
  two: "바나나",
};

// obj 문자열화 후 저장
const str = JSON.stringify(obj, null, 2);
const create = await executeQuery(`
    insert into test.test1 (jsonStringify) values (\'${str}\')
  `);

// obj 조회 후 JSON으로 다시 파싱
const get = await executeQuery(`select * from test.test1 where idtest1 = 2`);
const parse = JSON.parse(get[0].jsonStringify);
```

요렇게 하면 잘 문자열화 되어 저장이 되고 파싱도 원활하게 할 수 있다.
