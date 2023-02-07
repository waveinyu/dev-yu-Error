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

JSON.stringify는 JSON 데이터를 문자열로 변환하는 것이니 꼭 객체에 따옴표를 추가해주어야 한다.<br>

---

2023.02.07 DB 컬럼에 JSON.stiringify가 제대로 들어가지 않아도 위의 에러와 함께 파싱이 되지 않는다. 이번 경우는 어느 순간부터 특정 컬럼에 들어간 JSON.stiringify 값의 키와 밸류가 `???`으로 저장이 되어 있었다. utf-8도 제대로 설정이 되어 있었는데 우째서,,,<br>

![l_179323333_237_91d3f75284bd512cfa7e92d7fd915914](https://user-images.githubusercontent.com/99732695/217191734-5ffdcde2-f117-430d-a2ee-c738f3da88e2.png)

<br>

그 컬럼만 삭제하고 다시 만들어주니까 정상적으로 작동했다. 코드는 만진 적 없는데 되던 게 갑자기 안 돼서 디버깅하다가 요기서 잘못됐단 걸 알았다. 정말 DB는 너무 민감한 것 같다 . . .
