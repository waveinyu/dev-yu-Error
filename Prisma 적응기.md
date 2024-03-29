## Prisma 적응기(2023.05.03 ~ )

이번에 타입스크립트로 새로운 프로젝트를 시작하면서 데이터베이스를 어떻게 할까 잠시 생각했다. 이전에는 `mysql2`를 이용해서 로우쿼리로 진행했는데, 타입스크립트와는 ORM을 많이 쓰는 것 같았고(정확히는 `typeORM`) . . 새로운 걸 좀 해보고 싶기도 해서 고민을 많이 했다. <br>

항상 `typescirpt + typeorm` 조합을 들어서 그런지 프로젝트 열고 찾아보지도 않고 `typeORM`을 설치했는데, 현재 쓰고 있는 `Next.js` 프레임워크에 최적화된 건 아닌 건지 호환이 쉬워보이지 않아서 next.js orm을 검색하다가 `Prisma`로 결정하게 됐다. <br>

typeORM을 선택하지 않은 정확한 이유는<br>

1. `reflect-metadata`란 것을 설치하면서 전역(ex. `app.ts`)으로 불러와야 한다는 것. Next.js는 SSR이라 API를 API Routes로 생성하여 전역으로 부를 수 있는 파일(?)이 없어서 문제 발생

2. 위를 해결하고자 `typeORM`의 공식 이슈란에서 검색을 해봤더니 Next.js와는 최적화되지 않은 것으로 보였다. (설치부터 문제가 많았고 그게 최근까지 이어짐)

## Prisma

[Prisma `Get Started` - `EXISTING SQL PROJECT`](https://www.prisma.io/docs/getting-started/setup-prisma/add-to-existing-project/relational-databases-typescript-mysql)<br>
[Prisma schema reference(`Naming conventions`)](https://www.prisma.io/docs/reference/api-reference/prisma-schema-reference#naming-conventions)<br>
[Prisma Client API reference](https://www.prisma.io/docs/reference/api-reference/prisma-client-reference#findmany)

(추가예정)
<br>

## 사용하면서 느낀 장점

`schema.prisma` 파일에서 데이터베이스 스키마가 어떤 데이터 타입으로 구성되어 있는지 명시하고 있기 때문에, 타입스크립트로 지정한 컬럼의 데이터타입과 스키마의 데이터타입이 일치하지 않으면 바로 에러를 내뱉는다. <br>

`TS2322: Type 'number' is not assignable to type 'string'.` 나의 경우엔 이 에러였는데, Int로 설정했다고 생각한 컬럼이 다시 보니 varchar로 되어있었다. 그래서 타입스트립트에서 number로 타입을 지정해놔도 데이터타입이 일치하지 않기 때문에 에러가 났던 것<br>

<br>
프리즈마만 제공하는 기능인지는 모르겠지만 ORM을 쓰는 이유가 하나 더 늘었다..! 컬럼 두 개나 수정해서 얼마나 다행인지 @.@
