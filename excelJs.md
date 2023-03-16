전체 조회한 내용을 엑셀로 내보내는 기능이 있다. 엑셀로 만들기는 `excelJs`란 라이브러리를 사용해서 어렵지 않게 만들 수 있었는데, 클라이언트 측에서 다운 받는 과정(?!)이 일어나지 않았다. 그래서 좀 많이 헤매서 적어둔다..

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
| `Content-Disposition` | - 컨텐츠가 어떻게 처리되어야 하는지의 방식 명시<br>-                                                 |

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
