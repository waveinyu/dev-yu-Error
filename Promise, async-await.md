| 배포환경 |                          문제                           |    해결     |
| :------: | :-----------------------------------------------------: | :---------: |
| `Vecel`  | 배포 시 `nodemailer`를 사용한 메일 발송이 작동하지 않음 | 비동기 처리 |

---

참고 1 [[Vecel 공식문서] Can I use SMTP with Vercel?](https://vercel.com/guides/serverless-functions-and-smtp)<br>
참고 1-1 [[Vercel 공식문서] Serverless Function Execution Timeout](https://vercel.com/docs/concepts/limits/overview#serverless-function-execution-timeout)<br>
참고 2 [[Stack Overflow] Nodemailer in vercel not sending email in production](https://stackoverflow.com/questions/65631481/nodemailer-in-vercel-not-sending-email-in-production/75894841#75894841)<br>

회사에서 문의글을 등록하면 회사 gmail로 문의글이 등록됐다는 메일 알림 기능을 추가했는데, 로컬에서 작업할 때는 너무 잘 됐던 게 배포하니 전혀 작동하지 않았다. <br>

Vecel 공식문서를 보니, 버셀에서 권장한 서드파티 메일 서비스(sendGrid, AWS SES, MailChimp)가 아닌 SMTP를 직접 사용할 땐 `await`를 사용하여 비동기 처리가 필요하다고 언급되어 있었다. Vecel은 서버리스 환경으로, 서버리스 함수가 클라이언트에 응답을 보내는 순간 작업중인 완료되지 않은 채 일시정지 되기 때문에 `await`으로 작업을 제어해주어야 한다는 것.<br>

문제는 나는 이미 `async function`으로 메일을 보내는 함수를 선언했는데 전혀 되지 않는다는 거였다. 스택오버플로우의 솔루션들을 봤을 때도 크게 다를 바가 없었다.<br>

```javascript
async function sendMail(cateogry, info) {
    let transporter = nodemailer.createTransport({ ... })

     // verify connection
    await new Promise((resolve, reject) => { ... })

        ...

    // send mail
    await new Promise((resolve, reject) => {
        transporter.sendMail(message, (error, info) => {
        if (error) {
            console.error(error)
            reject(error)
        } else {
            console.log(info)
            resolve(info)
        }
        })
    })
}
```

위처럼 작성했는데, 디버깅을 해보니 배포 시 verify connection부터 콘솔값이 찍히지 않았다. 그러다가 찾은 해결은...!

```javascript
// sendMail() 호출

const handler(async (req, res)=>{

    ...
    await sendMail(category, info)

    })
```

`sendMail()`을 호출할 때 `async`를 붙여주는 거였다. 나의 경우는 이미 `sendMail()`을 비동기 함수로 만들었기 때문에 `await`를 쓸 생각을 못 했다. 결국 크게 보면 sendMail()의 작업이 다 끝나지 않은 채로 응답이 가게 되면 `await`로 작업을 제어시키면 되는 것이었다. 이미 `handler`도 비동기 함수이기 때문에 `await`가 사용이 가능한 것!<br>

같이 비동기 처리를 했는데도 스택오버플로우와 다르게 되지 않길래 진짜 아예 서드파티로 넘어가야 하나? 싶었는데 해결해서 좋았다. 해결한 후에 같은 팀원분이랑 다른 개발자분과도 얘기 나누고 나니 어제 처음 글 쓸 떄보다 더 이해가 잘 되는 것 같다.
