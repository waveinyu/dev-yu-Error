```javascript
const express = require("express");
const app = express();
const port = 1000;

const swaggerUi = require("swagger-ui-express");

const test1 = require("./swagger/test1.json");
const test2 = require("./swagger/test2.json");

// swagger router 분리
// https://github.com/scottie1984/swagger-ui-express/issues/65
// https://stackoverflow.com/questions/49607146/swagger-ui-express-module-instantiates-only-the-last-defined-document
const renewalAdminHtml = swaggerUi.generateHTML(test1);
app.use("/test1-api-docs", swaggerUi.serveFiles(test1));
app.get("/test1-api-docs", (req, res) => {
  res.send(renewalAdminHtml);
});

const branchManagerHtml = swaggerUi.generateHTML(test2);
app.use("/test2-api-docs", swaggerUi.serveFiles(test2));
app.get("/test2-api-docs", (req, res) => {
  res.send(branchManagerHtml);
});
```

api를 작성한 스웨거 문서를 html로 ui를 그리고 두 개 이상의 문서를 작성하기 위해 [swaggerUi.serveFiles()](https://www.npmjs.com/package/swagger-ui-express)를 사용한다.
