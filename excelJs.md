전체 조회한 내용을 엑셀로 내보내는 기능이 있다. 엑셀로 만들기는 `excelJs`란 라이브러리를 사용해서 어렵지 않게 만들 수 있었는데, 클라이언트 측에서 다운 받는 과정(?!)이 일어나지 않았다. 그래서 좀 많이 헤매서 적어둔다..!

<br>

## excelJs 구동 과정

#1 **새로운 워크북(엑셀) 생성**

```javascript
const workbook = new Excel.Workbook();
```

#2 **워크북에 시트 추가**

```javascript
const worksheet = workbook.addWorksheet(sheetName);
```

#3 **컬럼 추가(스타일 지정 가능, 우선적)**

```javascript
// 배열의 요소에 필요한 컬럼들 객체로 하나하나 추가
// 스타일은 컬럼에 적용할 공통 스타일 명시(workshieet.columns에서 지정한 게 가장 우선)
worksheet.columns = [
  {
    header: "Idx",
    key: "idx",
    width: 8,
    style: {
      font: { size: 11 },
      numFmt: "@",
      alignment: { horizontal: "center" },
    },
    ...
  },
];
```

#4 **로우 추가(배열의 요소를 컬럼 순대로 로우값 배치)**

```javascript
 worksheet.addRow([ idx, ...])
```

#5 **엑셀파일 생성**

```javascript
workbook.xlsl.write();
```

1~5까지는 원하는 엑셀 파일을 하나 만든 것이다. 이 파일을 유저가 다운받기 위해서는 클라이언트 측으로 넘겨주어야 한다. 이때, `res.setHeader`를 이용하여 **브라우저에 어떤 파일이 어떻게 넘어갈 것인지** 정보를 실어 넘겨주어야 한다. 이와 같이 정보에 대한 정보를 **메타 데이터**라고 한다.

<br>

#6 **헤더에 메타데이터 입력**

| header                | Description                                                                                          |
| --------------------- | ---------------------------------------------------------------------------------------------------- |
| `Content-Type`        | - 전송되는 컨텐츠가 어떤 유형인지 명시<br>- 브라우저는 해당 데이터를 어떻게 처리해야 하는지 판단<br> |
| `Content-Disposition` | - 컨텐츠가 어떻게 처리되어야 하는지의 방식 명시<br>                                                  |

```javascript
const fileName = "file_name";

res.setHeader("Access-Control-Expose-Headers", "Content-Disposition");
res.setHeader(
  "Content-Type",
  "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
);
res.setHeader(
  "Content-Disposition",
  "attachment; filename=" + fileName + ".xlsx"
);
```

나는 여기까지만 하면 된다고 생각했는데 아무리 해도 넘어가지가 않는 것이다.. 실 서버로 직접 다운로드로 해보면 아무 동작이 일어나지 않고, api 테스팅을 하면 response 값에서 모든 글씨가 다 깨져보였다.
<br>

<img src="https://user-images.githubusercontent.com/99732695/228994674-09a01fe2-848c-4b26-9b51-466f2cfcef5c.png" width=50%>

<br>

'한글 내용 때문에 깨진 건가?', '인코딩을 해야하나?' 하는 생각들이 많았지만 결과적으로는 브라우저에서 확인할 땐 아무런 액션이 없으니 인코딩은 그 다음 문제라고 생각했다. 클라이언트가 내려줘야 하지 않나? 란 생각을 계속 하긴 했었지만 `excelJs` 관련으로 구글링해보면 꼭 그런 것 같지도 않아서 헤맸다. <br>

그러다가 `excelJs` 공식 레포의 이슈란에서 해결방법을 찾을 수 있었다.

<br>

## 클라이언트

[How client(React.js) download excel file in NodeJS?](https://github.com/exceljs/exceljs/issues/1556)

```javascript
{
  // https://github.com/exceljs/exceljs/issues/1556 (클라이언트 코드)

  await axios
    .get(/api/test, {
      responseType: "arraybuffer", // !! IMPORTANT !!
    })
    .then((res) => {
      const fileName = res.headers["content-disposition"].split("=")[1];

      const blob = new Blob([res.data], {
        type: "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
      });
      saveAs(blob, fileName);

      if (blob) {
        alert("다운로드 완료");
      }
    });
}
```

블롭으로 클라이언트에서 내려받을 수 있게 처리했다. 해결법에서 `responseType: "arraybuffer"`이 중요하다고 했는데, 찾아보니 **Blob과 ArrayBuffer 객체는 서로 변환이 될 수 있다고 한다.**

<br>

스트림이랑 버퍼를 사용하면 백엔드에서 한 번에 처리할 수 있다고는 하는데 또 의문,,,, 그치만 아직은 여기까지만,,, 여러번 보다 보면 이해가 되는 날이 오겠지
