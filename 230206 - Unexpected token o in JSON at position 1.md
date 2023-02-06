```javascript
Unexpected token o in JSON at position 1
```

`JSON.stringify`로 저장한 데이터를 다시 `JSON.parse`로 파싱하면서 만난 에러<br>
테스트할 때 내가 임의로 넣었던 데이터가 JSON 형식에 맞지 않아서 발생했고 제대로 넣어주니 해결됐다.<br>

1. json 형식의 문자열이 맞는가?
2. json 형식의 문자열이 맞다면 오타가 있는가?

찾아보니 원인은 크게 저렇게 두가지 정도가 있다고 한다.

```javascript
const jsonStringify = '{"name": "루비", "age": 2, "weight": 4.5}';
```

JSON.stringify는 JSON 데이터를 문자열로 변환하는 것이니 꼭 객체에 따옴표를 추가해주어야 한다.
