## _error_

간단한 for문을 작성 중에 선언하지 않은 변수가 undefined라는 에러를 확인했다.

```javascript
TypeError: Cannot read properties of undefined (reading 'columnA')
```

<br>

작성 중이었던 for문

```javascript
const dataArr = [{ idx: 1, columnA: "dataA", columnB: "dataB" }, {} ... ];

for (let i = 0; dataArr.length; i++) {
  await executeQuery(`
      UPDATE tableName
      SET columnA = "${dataArr[i].columnA}"
      WHERE idx = ${dataArr[i].idx}
    `);
}
```

## _solution_

`"${dataArr[i].columnA}"` 요 부분에서 계속 에러가 났다. colmunA라고 했지만 해당 테이블에 있는 모든 컬럼을 넣어도 같은 에러를 뱉었다. 왜 그런가 했는데 . . . .

`for (let i = 0; dataArr.length; i++)`에서 `dataArr.length`의 범위를 지정하지 않았다. 이미 DB에서 조회한 result의 length는 한정적인데 `i`는 계속 증가하고 있기 때문이었다. 휴 속시원하다 아까는 정말 띠용이었는데...

<br>

```javascript
for (let i = 0; i < dataArr.length; i++) {
    ...
}
```

바르게 쓰면 정상적으로 작동이 된다. for문은 정말 자주 쓰는 구문이어서 저기는 캐치 못 했는데 앞으론 익숙한 것이어도 자세히 확인해봐야겠다.
