## _error_

remote: Permission to 저장소.git denied to 계정.<br>
fatal: unable to access ‘저장소주소': The requested URL returned error: 403<br>

이 전에도 비슷한(?) 에러를 ssh 키를 발급 받고 등록하여 해결했었는데 그건 [이 글](https://maliceit.tistory.com/51)을 참조<br>

이번 경우는, 회사 컴퓨터에서 github 최초 계정 등록을 회사 계정으로 했었다. 개인 레포 생성 후 push 하려고 하니 위와 같은 에러 발생 (접속자와 레포 생성 계정의 불일치)<br>

## _solution_

`제어판` > `모든 제어판 항목` > `자격 증명 관리자` > `Windows 자격 증명`

![image](https://user-images.githubusercontent.com/99732695/214462163-d053be49-40e2-4a08-8b69-737f37db8912.png)

- 이 항목에 들어가면 `일반 자격 증명` > `git.https://github.com` 란이 보인다.

<br>

토글을 열어 push 하고자 하는 정보를 변경해준다.

![image](https://user-images.githubusercontent.com/99732695/214462184-e4afc38e-5ea2-45c1-b3f1-9c8ab30a0752.png)

- 회사계정을 나의 계정으로 변경<br>

그리고 푸쉬하면 정상적으로 반영이 된다.<br>
[참고 블로그](https://recoveryman.tistory.com/392)
