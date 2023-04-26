DB 서버로 라이트세일을 사용하고 있으면 DB config는 모두 라이트세일에서 제어할 수밖에 없다는 걸 알면서 자꾸 까먹는다 . . . 그래서 아예 리드미에 따로 목차를 만들었다.<br>

## problem

라이트세일의 데이터베이스 서버의 파라미터를 확인하기 위해서는 `AWS cli`와 `터미널`을 이용해야 한다.

```javascript
// aws-cli lightsail parameters
aws lightsail get-relational-database-parameters
```

이렇게 명령어를 입력하면<br>

```termianl
C:\Users\USER>aws lightsail get-relational-database-parameters --relational-database-name 데이터베이스명
{
    "parameters": [
        {
            "allowedValues": "0,1",
            "applyMethod": "pending-reboot",
            "applyType": "dynamic",
            "dataType": "boolean",
            "description": "Automatically set all granted roles as active after the user has authenticated successfully.",
            "isModifiable": true,
            "parameterName": "activate_all_roles_on_login",
            "parameterValue": "0"
        },
.....
        {
            "allowedValues": "0,1",
            "applyMethod": "pending-reboot",
            "applyType": "static",
            "dataType": "boolean",
            "description": "Controls whether the server autogenerates SSL key and certificate files in the data directory, if they do not already exist.",
            "isModifiable": false,
            "parameterName": "auto_generate_certs"
-- More  --
```

이렇게 나온다. 마지막의 `-- More  --`이란. . . **끝없이 엔터를 쳐야 나머지 파라미터가 나온다.** 엄청 긴데 그걸 마냥 엔터 쳐서 얻을 수는 없는 일이고, 그렇다고 AWS lightsail의 공식문서에 파라미터 리스트가 있는 것도 아니었다. (일단 엄청 뒤져봤지만 나는 못 찾았음)

<br>

그래서 터미널에서 해당 내용을 출력해서 파일로 내보내는 방법은 없을까? 해서 찾아봤는데 다행히도 있었다.

## solution

[커맨드 출력을 파일로 저장(Save the output of a command to a file)](https://mhilt767.tistory.com/entry/CMD%EC%BB%A4%EB%A7%A8%EB%93%9C-%EC%B6%9C%EB%A0%A5%EC%9D%84-%ED%8C%8C%EC%9D%BC%EB%A1%9C-%EC%A0%80%EC%9E%A5-Save-the-output-of-a-command-to-a-file)

<br>

#1. 명령어 뒤에 `>filename.txt` 붙여주기

```terminal
aws lightsail get-relational-database-parameters --relational-database-name 데이터베이스명 > lightsail.txt
```

나는 파일명을 lightsail로 지정했다.

<br>

#2. 터미널에서 바로 파일 생성 확인하기

```terminal
notepad lightsail.txt
```

새창으로 텍스트 파일이 뜨는 거 확인하면 끝! (근데 진짜 해결해야 할 문제는 아직도 남았다 . . . 쩜쩜)
