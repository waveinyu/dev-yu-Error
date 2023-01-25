## _error_

remote: Permission to 저장소.git denied to 계정.<br>
fatal: unable to access ‘저장소주소': The requested URL returned error: 403<br>

이 전에도 비슷한(?) 에러를 ssh 키를 발급 받고 등록하여 해결했었는데 그건 [이 글](https://maliceit.tistory.com/51)을 참조<br>

이번 경우는, 회사 컴퓨터에서 github 최초 계정 등록을 회사 계정으로 했었다. 개인 레포 생성 후 push 하려고 하니 위와 같은 에러 발생 (접속자와 레포 생성 계정의 불일치)<br>

`제어판` > `모든 제어판 항목` > `자격 증명 관리자` > `Windows 자격 증명`

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d1ed5cdf-9fa3-4d5b-86dd-7f9bfef03cba/Untitled.png)

- 이 항목에 들어가면 `일반 자격 증명` > `git.https://github.com` 란이 보인다.

<br>

토글을 열어 push 하고자 하는 정보를 변경해준다.

![제목 없음.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/10e44e61-517f-4156-9043-61b5b7fad289/%EC%A0%9C%EB%AA%A9_%EC%97%86%EC%9D%8C.png)

- 회사계정을 나의 계정으로 변경<br>

그리고 푸쉬하면 정상적으로 반영이 된다.<br>
[참고 블로그](https://recoveryman.tistory.com/392)
