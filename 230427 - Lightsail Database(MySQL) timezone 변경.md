이전 글 : [230426 - LIGHTSAIL DATABASE PARAMETERS](https://github.com/waveinyu/dev-yu-Error/blob/main/230426%20-%20LIGHTSAIL%20DATABASE%20PARAMETERS.md)

<br>

|    컬럼명     | 데이터타입 | 사용함수 |
| :-----------: | :--------: | :------: |
| `createdDate` | `DATETIME` | `now()`  |

실서버에서 테스트 하는 중에 시간이 `SYSTEM`이 아닌 `UTC`로 설정된 것을 확인했다. (나중에 더 찾아보니 내가 워크벤치에서 조회해 본 `time_zone`은 로컬 MySQL 서버라 `SYSTEM`이었고, 문제가 있는 서버는 `LIGHTSAIL`의 서버였다.)

<br>

[Amazon Lightsail에서 데이터베이스 파라미터 업데이트](https://lightsail.aws.amazon.com/ls/docs/ko_kr/articles/amazon-lightsail-updating-database-parameters)<br>
한국 시간으로 바꾸기 위해서는 라이트세일 DB 파라미터 `time_zone`을 `Asia/Seoul`로 변경해주어야 한다.

```terminal
aws lightsail update-relational-database-parameters --relational-database-name 데이터베이스명 --parameters "parameterName=time_zone,parameterValue=Asia/Seoul,applyMethod=immediate"
```

위를 진행하면,

<br>

```terminal
C:\Users\USER>aws lightsail update-relational-database-parameters --relational-database-name 데이터베이스명 --parameters "parameterName=time_zone,parameterValue=Asia/Seoul,applyMethod=immediate"
{
    "operations": [
        {
            "id": "고유아이디",
            "resourceName": "데이터베이스명",
            "resourceType": "RelationalDatabase",
            "createdAt": "2023-04-26T12:48:34.074000+09:00",
            "location": {
                "availabilityZone": "ap-northeast-2a",
                "regionName": "ap-northeast-2"
            },
            "isTerminal": true,
            "operationDetails": "",
            "operationType": "UpdateRelationalDatabaseParameters",
            "status": "Succeeded",
            "statusChangedAt": "2023-04-26T12:48:34.074000+09:00"
        }
    ]
}
```

이렇게 해당 결과를 터미널에서 확인할 수 있다.

<br>

![image](https://user-images.githubusercontent.com/99732695/234733085-8293a377-6f4e-4a3e-9abd-b6394243c4d7.png)<br>
워크벤치에서도 이제 해당 DB 서버의 타임존이 `Asia/Seoul`인 걸 확인할 수 있다.
