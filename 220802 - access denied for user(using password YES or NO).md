## _error_

```javascript
access denied for user 'root'@'localhost' (using password YES / NO)
```

## _solution_

**Case 1. YES**<br>
비밀번호가 틀린 경우라고 한다.

- 근데 구글링을 해보니까 딱히 진짜 비밀번호가 틀린 경우가 아니더라도 발생하는 에러였음(?? 정신차려)
- 흔히 권한 부여, 비밀번호 수정 이렇게 해결을 하는 거 같은데 나는 그게 하나도 안 먹혔어서 이 에러를 4시간 동안 잡고 있었다. 왜냐면 이 단계가 지나가지 않으면 다음 강의도 못 들었기 때문에 . . .
- 도커에서도 서버 종료하고 VSCode에서 mySQL 연결을 다 끊어봐도 127.0.0.1에 살아있는 녀석이 있었는데 걔를 찾아보니까 아니 글쎄 mySQL.exe였지모야..(^^)
- 제어판에 잡히지 않아도 `작업관리자 - cpu - 리소스모니터`를 보면 돌아가는 녀석들이 분명히 보인다. 나를 믿지 말자 . . . 는 좀 슬프니까 그냥 **컴퓨터를 믿자 . . .**

<br>

**Case 2. NO**<br>

- 이건 내 문제가 아니라 조원의 문제였는데,

- 성공적으로 도커와 mySQL 모두 연결해놓았는데 squelize로 DB를 생성하려고 하면 Access Denied (using password : NO) 가 뜨는 것 . . . (어째서?!)

- 역시 구글링 했을 땐, YES가 비밀번호가 틀린 문제라면 NO는 비밀번호를 입력하지 않은 문제라고 하는데 입력을 잘 했는데도 (config.json - 비밀번호) 안 됐던 상황이었다.

- 해결은 .. 프로젝트가 있던 파일명이 한글이었는데 그걸 영문으로 바꾸니까 잘 됐다.

- 아마 기존에 연결해놓은 mySQL이랑 sequeilize랑 뭔가 경로 연결(?!)이 잘 안 돼서 그랬던 것 같다고 어렴풋이 생각은 하는데..

- 조원이 이 파일명 때문에 그런 것 같다며, 접근해보지 않았다면 또 그 에러로 오랜 시간 동안 잡고 있었을 것 같다. 결국 이것도 비밀번호 문제는 아니었고? 물론 이 경우엔 경로를 못 잡아서 비밀번호가 없다시피 작용한 것 같지만.<br>

  구글에서 찾아봤을 땐 node.js와 npm, sequelize 모두 버전의 문제일수도 있다고 했었기 때문에 나는 전혀 경로 문제라곤 생각하지 못했고..근데 그것보다도 이미 상위 폴더에서도 한글명으로 폴더를 만들었기 때문에 근본적인 원인을 찾지는 못했다는 게 결론. 해결은 했지만..

<br>
이번 에러로 느낀 점 <br>

- 액세스 디나이드라고 해서 순진하게 진짜 비밀번호 문제라고만 생각하면 안 된 다는 것,,
  에러 메시지는 단순히 힌트일 뿐 진짜 그 자체는 아니라는 생각을 가져야 겠다. 어떻게 저기에서 실행 파일이 없는데(분명히!!!) 있다고 생각해서 지울 생각을 하고 경로 문제라는 걸 생각해낼 수 있었겠어