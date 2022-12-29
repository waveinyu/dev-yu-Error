## _error_

---

```javascript
*Error: You cannot use both an optional and required catch-all route at the same level ("[[...id]]" and "[...id]")*
```

api 폴더에 `[id].js` / `[…id].js` / `[[…id]].js` 전부 만들어 놓고 테스트 해보다가 보게 된 에러

<br>

## _solution_

---

Catch-all route는 같은 레벨에서 1중과 2중을 같이 쓸 수 없다. 둘 중 한 파일을 삭제하니 정상적으로 작동됨.
