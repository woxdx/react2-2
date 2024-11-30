# 이원도 202230226
## 11월 20일
### use client 란?
- Next.js 13에서 도입된 새로운 지시어로, 컴포넌트가 클라이언트 사이드에서만 렌더링된다는 것을 명시

- Server Components와 Client Components가 있는데 이 둘을 구분하기 위함이다.

### use client 주요 특징과 사용 이유
- 동적 데이터와 상호작용 : 실시간 채팅, 동적 폼 검증, 클라이언트 사이드에서만 가능한 계산이나 데이터 처리 등 클라이언트 사이드에서 처리해야 하는 동적인 데이터와 상호작용에 적합함

- 브라우저 API 접근 : 클라이언트 사이드 렌더링을 통해 브라우저의 API, 예를 들어 window나 navigator와 같은 글로벌 객체에 접근할 수 있다. 이는 서버 사이드에서는 불가능한 작업

- 성능 최적화 : 필요한 경우에만 클라이언트 사이드 렌더링을 사용함으로써, 서버의 부담을 줄이고 전체 애플리케이션의 성능을 최적화할 수 있다. 특히, 초기 페이지 로드 시 필요하지 않은 리소스의 로딩을 방지할 수 있다.

### 주요 Directory & File
- app : Routing Page 관리

- components : 재사용 가능한 공통 컴포넌트 관리

- features : 재사용이 불가능하거나 많은 수정을 해야 하는 컴포넌트 관리

- context : 기능별 컴포넌트 관리

- store : Redux store 설정 파일 관리

- styles : CSS, Sass 등 스타일 파일 관리

### Redux
- Slice

  - Redux Toolkit에서 사용되는 용어로, 특정 기능과 관련된 상태와 Reducer를 한 곳에서 정의하는 곳
### Provider

  - Redux Provider는 Redux의 상태 등을 공급하기 위한 파일입니다.

  - Provider를 사용하고자 하는 Page에서 사용하면 됩니다.

  - 다만 전역적으로 사용할 때는 layout 파일에 정의하면 를 사용해야 하기 때문에 별도위 컴포넌트를 만들어서 사용하는 것이 좋다.

### Context API 와 Redux 비교
- Context API

  - React에서 기본으로 제공하는 상태 관리 도구로, 외부 라이브러리 설치 없이 사용 가능

  - Context API는 주로 전역 상태를 관리하는 데 사용

  - React.createContext()로 생성한 Context 객체와 Provider

  - 장점

  - 단점

- Redux

  - Redux는 전역 상태를 관리하기 위한 독립적인 state 관리 라이브러리

  - 상태의 변경을 예측 가능하게 하고, 전역 state 관리를 더 구조적으로 지원

  - store, reducer, action 등의 개념을 사용해 state와 state dispatch를 관리

  - 장점

    - 명확한 상태 관리 구조

    - 미들웨어 지원

    - 디버깅 도구

    - 모든 프레임워크와 호환

- 단점

    - 설정 코드의 복잡도

    - 추가 라이브러리 필요

    - 작은 애플리케이션에는 과한 설정
## 11월 13일
### Props 흐름의 이해
- Next.js의 데이터 흐름은 단방향으로 이루어 집니다.
- 즉, parents에서 child componet의 방향으로 props의 흐름이 이루어 집니다.
- 따라서 계층 구조가 복잡해 지면 Props Drilling 문제가 발생합니다.
- Props Drilling은 여러 개의 component를 지나 props가 전달 되면서 발생하는 문제입니다.
### Props Drilling은 다음과 같은 문제를 발생 시킬 수 있습니다.
1. 중간에 위치한 component에 불필요한 props를 전달해야 하는 문제
2. 타겟 component까지 props가 전달되지 않을 경우 원인 규명의 어려움
3. 필요 이상으로 코드가 복잡해 지는 문제
- 이런 문제를 해결하려면 Props를 전역으로 사용하면 됩니다.
- Next.js에서 props를 전역으로 사용하기 위해서 context api, redux 등을 사용합니다.

### Context API
- context는 UX 구축에 많이 사용되는 React의 기능

- React는 16.3 버전부터 정식적으로 context api를 지원

- 일반적으로 props는 부모에서 자식으로 전달되는 단방향 통신을 합니다.

- Context API는 특정 component가 props를 사용하지 않고, 하위 component를 포함한 모든 component에 데이터를 공유할 수 있는 기능을 제공

- 즉, 전역으로 데이터를 사용할 수 있도록 해줌

- Context API는 createContext, Provider, useContext 개념만 알면 적용이 가능

### 예시 코드 (다크모드 / 라이트모드)
- src/contexts/ThemeContext.jsx
```js
"use client";

import { createContext, useEffect, useState } from "react";

export const ThemeContext = createContext();

export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState("light");

  useEffect(() => {
    const initialTheme = theme === "light" ? "light" : "dark";
    setTheme(initialTheme);
    document.body.className = initialTheme;
  }, []);

  const toggleTheme = () => {
    const newTheme = theme === "light" ? "dark" : "light";
    setTheme(newTheme);
    document.body.className = newTheme;
  };

  return <ThemeContext.Provider value={{ theme, toggleTheme }}>{children}</ThemeContext.Provider>;
};
```
- src/components/ThemeToggleButton.jsx
```js
"use client";

import { ThemeContext } from "@/contexts/ThemeContext";
import { useContext } from "react";

const ThemeToggleButton = () => {
  const { theme, toggleTheme } = useContext(ThemeContext); // ThemeContext로부터 값 가져오기

  return <button onClick={toggleTheme}>{theme === "light" ? "Dark Mode" : "Light Mode"}</button>;
};

export default ThemeToggleButton;
```

- src/app/layout.tsx
```js
import ThemeToggleButton from "@/components/ThemeToggleButton";
import { ThemeProvider } from "@/contexts/ThemeContext";
import "@/app/styles/global.css";

/* ...생략 */

export default function RootLayout(props: { children: React.ReactNode }) {
  const { children } = props;
  return (
    <html lang="ko" suppressHydrationWarning className={`${geistMono.variable} ${geistSans.variable}`}>
      <body>
        <ThemeProvider>
          <ThemeToggleButton />
          {children}
        </ThemeProvider>
      </body>
    </html>
  );
}
## 11월 6일
## UI 라이브러리
- UI 라이브러리, 프레임워크, 유틸리티는 필수 X

- 생산성 향상 및 UI 일관성

- chakra UI

    - 버튼, Modal, 입력 등 다양한 내장 컴포넌트 제공

    - dark mode, light mode 지원

    - 타입스크립트로 작성되어 있음
```js
npm i @chakra-ui/react @emotion/react
npx @chakra-ui/cli snippet add
```
- TailwindCSS

    - 다른 프레임워크와는 다르게 CSS 규칙만을 제공

    - JS 모듈이나 react 컴포넌트를 제공하지 않기 때문에 필요한 경우 직접 만들어서 사용해야 함

    - 변수값을 조정하여 개성있는 디자인을 만들 수 있음. 디자인 자유도가 높다.

    - dark mode 및 light mode를 쉽게 적용할 수 있다.

    - 빌드 시점에 사용하지 않는 클래스는 제거 되기 때문에 높은 수준의 최적화를 지원

    - CSS 클래스의 접두사를 활용해서 모바일, 데스크톱, 태블릿 화면에서 원하는 규칙을 지정할 수 있음

- `예시코드 <div className="sm:hidden md:flex lg:inline-block"></div>`
```js
sm => @media (min-width: 640px) { ... }
md => @media (min-width: 768px) { ... }
lg => @media (min-width: 1024px) { ... }
xl => @media (min-width: 1280px) { ... }
2xl => @media (min-width: 1536px) { ... }
```
- 현재는 `TailwindCSS`와 `TailwindUI`를 지원함

- Headless UI

    - TailwindCSS를 만든 Tailwind Labs 팀의 무료 오픈소스 프로젝트

    - TailwindCSS는 웹 컴포넌트 안에서 사용할 수 있는 CSS클래스만 제공함

    - Headless UI는 CSS클래스를 제공하는 것이 아닌 동적 컴포넌트만 제공한다.

```js
npm install @headlessui/react
```
## 10월 30일
### CSS와 내장 스타일링 메서드
- Styled JSX

    - Styled JSX는 CSS-in-JS 라이브러리이다.

    - 내장 모듈이기에 설치가 필요 없음

    - "use client";
```js
export default function StyledJsx2() {
  return (
    <>
      <button className="button">버튼</button>
      <span>Span Tag</span>
      <style jsx>
        {`
          span {
            background-color: blue;
            color: white;
            font-size: 1rem;
          }
        `}
      </style>
    </>
  );
}
```
- 단점

    - IDE나 코드 편집기 등 개발 도구에 대한 지원이 부족

    - 문법 하이라이팅, 자동 완성, 린트(lint)기능을 제공 X

    - 코드 내에서 CSS에 대한 의존성이 점점 커지기 때문에 앱 번들도 커지고 느려짐

- CSS Module

    - CSS-in-JS의 단점을 보완하기 위한 방법이다.

    - .module.css 로 끝나는 파일에서 CSS클래스를 가져와서 사용

    - 변환한 객체에서 모든 키는 클래스 이름을 의미함

    - 클래스들은 컴포넌트 스코프를 가진다.

    - 생성된 HTML 태그를 보면 class 가 고유한 값을 갖는다.
```js
/* styles/globals.css */

.foo {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  gap: 32px;
  grid-row-start: 2;
  color: yellow;
}
```
```js
/* layout.js */

import "./styles/globals.css";
```
```js
/* page.js */

"use client";

export default function Root() {
  return (
    <div className="foo">
      <h1>Root Page</h1>
    </div>
  );
}
```
- CSS Module 상속
```js
/* styles/my.module.css */

.main {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  gap: 32px;
  grid-row-start: 2;
  color: red;
}
.main1 {
  composes: main;
  color: #cacaff;
}
/* page.js */
```
```js
import styles from "./styles/my.module.css";

export default function Root() {
  return (
    <div className={styles.main1}>
      <h1>Root Page</h1>
    </div>
  );
}
```
- SASS

- Next에서 기본으로 지원하는 전 처리기

- 단 패키지 설치가 필요함 ( npm install sass )

- SASS 및 SCSS(Sassy CSS) 문법으로 CSS Module을 만들고 사용할 수 있다.

- styles/Home.module.css 파일 이름을 styles/Home.module.scss로 바꿔주면 된다.

```js
// styles/foo.module.scss

$foo: red;

.bar {
  font: 500;
  color: aqua;
}
```
```js
import styles from "./styles/foo.module.scss";

export default function Root() {
  return (
    <>
      <div className="foo">
        <h1>Home_foo</h1>
      </div>

      <div className={styles.bar}>
        <h1>Home_foo1</h1>
      </div>
    </>
  );
}
```
## 10월 23일
### 누적 레이아웃 이동 (CLS: Cumulative Layout Shift)
- 정적 자원 중 이미지 파일은 SEO에 많은 영향을 미친다.

- 다운로드 시간이 많이 걸리고, 렌더링 후에 레이아웃이 변경되는 등 UX에 영향을 미친다.

- Image 컴포넌트를 사용하면 해결

- lazy loading : 이미지 로드 시점을 필요할 때까지 지연시키는 기술

- 이미지 사이즈 최적화로 사이즈를 1/10이하로 줄여줌

- placeholder를 제공

### Image Component
- local 방식
- 예시 코드
```js
import Image from "next/image";
import foo from "/public/images/leaf-6760484_1920.jpg";

export default function About() {
  return (
    <>
      <h1>About page</h1>
      {/* 경로 방식 */}
      <Image
        src="/images/corn-9064747_640.jpg"
        alt="옥수수"
        width={400}
        height={500}
      />
      <Image
        src="/images/corn-9064747_640.jpg"
        alt="옥수수"
        width={400}
        height={500}
        layout="responsive"
      />
      {/* import 방식 */}
      <Image src={foo} alt="단풍" width={400} height={500} />
    </>
  );
}
```

### Image 컴포넌트 사용 시 주의 사항

- width, height는 필수이다. (layout="fill"을 사용 시에는 생략)

- layout="responsive" 는 브라우저 크기에 맞게 가변한다.

## #Remote 방식
- Pixabay와 같은 외부 이미지를 사용하려면 next.config.mjs 설정이 필요

수정된 코드

```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "cdn.pixabay.com",
      },
    ],
  },
};

export default nextConfig;
```
- 예시 코드
```js
import Image from "next/image";

export default function About() {
  return (
    <>
      <h1>About page</h1>
      {/* Remote 방식*/}
      <Image
        src="https://cdn.pixabay.com/photo/2023/11/03/12/22/toadstool-8362901_1280.jpg"
        width={300}
        height={500}
        alt="버섯"
      />
    </>
  );
}
```
### 코드 구성과 데이터 불러오기
- 프로젝트를 시작할 때 확장과 복잡도에 대비 해야한다.

- 코드를 더 효율적으로 구성하기 위해 아토믹 디자인 원칙에 따라 디렉토리를 구성한다.

    - atoms : 가장 기본적인 컴포넌트 관리

    - molecules : atoms에 속한 컴포넌트 여러 개를 조합하여 복잡한 구조로 만든 컴포넌트 관리

    - organisms : molecules와 atoms를 섞어서 더 복잡하게 만든 컴포넌트 관리

    -templates : 위의 모든 컴포넌트를 어떻게 배치 결정해서 사용자가 접근할 수 있는 페이지

## 10월 4일
### Page Project Layout - app
- app.jsx는 서버에 요청할 때 가장 먼저 실행되는 컴포넌트입니다.
- 페이지에 적용할 공통 레이아웃을 선언하는 곳입니다..
- 기본 코드는 다음과 같습니다
```js
import "@/styles/globals.css";
exprot default function App([Component, pageProps]) {
    return <Component (...pageProps)/>;
}
```
- Global CSS는 이곳에 추가됩니다.
- props 중 Component는 서버에 요청한 페이지입니다.
- pageProps는 App.getlnitialProps를 이용하여 prefetching된 데이터를 반환합니다.
- 데이터가 없다면 빈 객체를 반환합니다.
- 단, getStaticProps, getServerSideProps와 같은 Data Fetching methods는 동작하지 않습니다.

### Page Project Layout - document
- document.jsx는 app.jsx 다음에 실행됩니다.
- 각 페이지에서 공통적으로 사용될 html, head, body 안에 들어갈 내용을 선업합니다.
- onClick 같은 이벤트나 CSS는 이 곳에 선언하지 않습니다.
- 만일 로직이나 스타일이 필요하다면 app.jsx에 선언해야 합니다.
- 기본 코드는 다음과 같습니다.
```js
import {Html,Head,Main,NextScript} from "next/document"
exprot default function Document() {
    return(
        <Html lang="ko">
         <Head>
         </Head>
         <body>
         <Main/>
         <NextScript/>
         </body>
        </Html>
    )
}
```
### Page Project Layout - layout.jsx
- layout.jsx는 app 디렉토리 아래에 위치합니다.
- layout.jsx는 Page Project에서 사용하던 app.jsx와 document.jsx를 대체합니다.
- 이 파일은 삭제해도 프로젝트를 실행하면 자동으로 다시 생겨납니다.
- 프로젝트를 생성할 때 생성된 코드는 다음과 같습니다.
```js
export const metadata = {
    title: 'Next.js'
    description: 'Generated by Next.js',
}

export default function RootLayout({ children }) {
  return (
    <html lang="ko">
      <body className={inter.className}>{children}</body>
    </html>
  );
}

```

### Link vs a vs router.push
- Link component를 이용해서 Navibar componet를 만들어봅니다.
- `<a>` tag는 html 동기식으로 전체가 reload 되기 때문에, 외부 링크를 할 때 사용합니다.
- 일반적으로 내부 링크 이동시에는 사용하지 않는 것이 좋습니다.
- router.push는 빌드 후, 이동할 주소가 html 상에 노출되지 않기 때문에 SEO에 취약합니다.
- link 컴포넌트는 빌드 후, a tag로 자동 변환됩니다.
- a tag의 장점인 SEO 최적화, Prefetch 가능, 우 클릭 기능 등을 갖습니다.
- 내부 페이지로의 이동할 때 이 방식을 사용해야 SPA 방식으로 전체 html중 필요한 부분만 비동기식으로 리렌더링 된다.
- 따라서 특별한 경우가 아니면 link 컴포넌트 사용을 권장합니다.


### 정적 자원 제공
- 정적 자원은 이미지, 폰트, 아이콘, 컴파일한 css, js 등으로 /public 디렉토리 안에 저장합니다.
- 자세한 내용은 4장에서 다시 살펴봅니다.
- 정적 자원 중 이미지 파일은 SEO에 많은 영향을 미칩니다.
- 불러오는데 많은 시간이 걸리고, 불러온 후에도 이미지 주변의 레이아웃이 변경되는 등 ux관점에서 좋지 않은 영향을 줍니다.
- 이를 누적 레이아웃 이동(CLS)라고 합니다.
- 사용자가 2번째 문단을 읽고 있었다면, 위치가 바뀌어도 읽던 곳을 놓칠 수 있습니다.
- imagae 컴포넌트를 사용해서 이와 같은 CLS문제를 해결합니다.

### 자동 이미지 최적화
- Next.js 10부터 image 컴포넌트를 사용해서 이미지를 자동으로 최적화할 수 있습니다.
- 이 기능을 사용하면 이미지를 WebPage와 같은 최신 이미지 포맷으로 제공할 수 있습니다.
- 최신 포맷을 지원하지 않는 브라우저의 경우에는 png나 jpg와 같은 예전 이미지 포맷도 제공합니다.
- 필요한 경우 이미지 크기를 조정할 수도 있습니다.
- 특히 클라이언트가 이미지를 요구할 때 최적화 작업을 한다는 장점이 있습니다.
- 따라서 Unplash나 Pexel과 같은 외부 이미지 서비스로 이미지를 제공할 수 있습니다.
- 먼저 next.config.js의 images 속성에 다음과 같이 서비스 호스팅명을 추가합니다.

## 10월 2일
### 동적 라우팅 상세
- [slag] (단일 동적 경로 : Dynamic Route Segment)

    - 단일 동적 세그먼트를 처리

    - 파일구조 : app/posts/[slug]/page.js

```js
export default function Post({ params }) {
  const { slug } = params;

  return <div>Post: {slug}</div>;
}
```
- [...slag] (다중 동적 경로 : Catch-All Dynamic Route)

    - 0개 이상의 경로 세그먼트를 배열로 처리

    - 세그먼트 값이 없으면 404 Bad Request 에러 발생

    - 파일구조 : app/posts/[...slug]/page.js
```js
export default function Post({ params }) {
  const { slug } = params;

  return <div>Slug segments: {slug ? slug.join("/") : "No slug"}</div>;
}
```
- [[...slag]] (선택적 동적 경로 : Optional Catch-All Dynamic Route)

    - 경로를 선택적으로 지정한다. (즉, 경로 세그먼트가 없어도 해당 경로로 접근할 수 있습니다)

    - 파일구조 : app/posts/[[...slug]]/page.js
```js
export default function Post({ params }) {
  const { slug } = params;

  if (!slug) {
    return <div>All posts</div>;
  }

  return <div>Slug segments: {slug.join("/")}</div>;
}
```

## 9월 25일
### Next.js 기초와 내장 컴포넌트
### 클라이언트와 서버에서의 라우팅 시스템 작동 방식
- Next는 파일시스템 기반의 페이지 라우팅과 앱 라우팅을 함

- 페이지 라우팅은 /pages 디렉토리 안의 index.js, index.jsx, index.tsx 파일에서 export한 React 컴포넌트

- 앱 라우팅은 src/app 디렉토리 안에 page.js, page.jsx, page.tsx의 파일에서 export한 React 컴포넌트

- 동적 라우팅 규칙을 만들려면 페이지 라우팅은 [slug].js 파일, 앱 라우팅은 [slug] 디렉토리가 필요

- [slug].js 는 매개변수로 사용되며 주소창에서 입력하는 값을 모두 받을 수 있다.

- 동적 라우팅 규칙은 중첩도 가능

- 접근 경로를 ~/posts/[data][slug] 형태로 받을 수 있다.

- 예시 : app/foo/[fooId]/page.jsx (경로)

```js
export default async function fooId(props) {
  console.log(props);

  return (
    <h1>
      foo {props.params.fooId} / {props.searchParams.country}
    </h1>
  );
}
```
URL : http://localhost:3000/foo/2?country=KOR

### 페이지 간 이동 최적화
- Next.js 가 정적 자원을 제공하는 방법

- 자동 이미지 최적화와 새로운 Image 컴포넌트를 사용한 이미지 제공 최적화 기법

- 컴포넌트에서 HTML 메타 데이터를 처리하는 방법

- _app.js와 _document.js 파일 내용 및 커스터마이징 방법
## 9월 11일

### 파이프라인 문법
```js
console.log(Math.random() * 10);

// 파이프라인 연산자를 사용하면 위 코드를 아래와 같이 바꿀 수 있습니다.

Math.random() |> (x) => x * 10 |> console.log;
```
- ECMAScript 내의 기술 위원회인 TC39에서 이 연산자를 공식적으로 채택하지는 않았지만 바벨 덕분에 사용할 수 있다.

- Next.js 앱에서 파이프라인 연산자를 사용하려면 npm으로 바벨 플러그인을 설치해야 한다.

### Transpile의 동작
- Babel

- ECMAScript 같은 js 최신 버전이나, TypeScript 이전 버전의 코드로 변환 시키는 Transpile 도구이다.

- AST(Abstract Syntax Tree)

- 소스 코드를 추상화된 트리 구조로 나타낸 것
- Babel의 parser는 js 코드를 컴퓨터가 이해하기 쉽게 변환해준다.

- 싱글 쓰레드로 실행되기 때문에 속도가 느리다는 단점

### SWC

- Next 12 이후 Babel에서 SWC로 교체되었다.

- SWC는 Rust로 작성되어 속도가 훨씬 빠르다

- ※ 속도가 빠른 이유는 Rust에는 WASM(WebAssembly)이라는게 있는데 이게 어셈블리어로 되어있다.(저급언어)

### 렌더링 전략
- 서버 사이드 렌더링(SSR)
- APM을 사용하는 웹 페이지 생성

- 자바스크립트 코드 적재되면 동적으로 페이지 내용을 렌더링한다.

- Next.js도 동적으로 페이지를 렌더링할 수 있다.

- 리액트 하이드레이션 :

- 서버에서 렌더링된 HTML 마크업에 기반하여 클라이언트 측에서 자바스크립트 이벤트와 상태를 연결하는 과정을 말한다.
- ### 장점

- 안전한 웹 애플리케이션 : 인증 또는 민감한 작업을 서버에서 수행하고 그 결과만 브라우저에 제공해 위협을 피할 수 있다.

- 뛰어난 웹 사이트 호환성 : 클라이언트 환경이 오래된 브라우저도 서비스를 제공한다.

- 뛰어난 SEO : 서버가 렌더링한 HTML을 받기에 웹 크롤러가 페이지를 렌더링할 필요가 없음

- ### 단점

- 페이지간의 이동은 CSR에 비해 느리다.

- 서버 과부하

- 깜빡임 이슈 (매번 새로고침 해야하기 때문에)

- SSR은 만능이 아니다.

- 단순히 서버가 SPA(Single Page App)를 렌더링한다고 모든 것이 해결되지 않음.
- 오히려 자바스크립트 코드를 증가시키며, 애플리케이션이 인터렉티브 할 때까지 걸리는 시간이 단순 클라이언트 렌더링보다 더 길어질 수 있다.
- 클라이언트 사이드 렌더링(CSR)
실제 렌더링이 클라이언트에서 이루어지는 방식

- React 앱을 실행하면 렌더링 시작 전에 빈 화면이 한동안 유지 되는 것이 보임

- ### 장점

- 네이티브 앱처럼 느껴지는 웹 앱

- 전체 자바스크립트 번들을 다운로드 한다는 것은 렌더링할 모든 페이지가 이미 브라우저에 다운로드 되어 있다는 뜻

- 다른 페이지로 이동해도 서버에 요청할 필요 없이 바로 이동

- 페이지를 바꾸기 위해 새로 고침이 없다.

- ### 쉬운 페이지 전환

- 클라이언트에서 네비게이션이 새로고침이 발생하지 않아 UX에 도움이 된다.
지연된 로딩과 성능

- 초기 로딩 이후 빠른 웹사이트 렌더링이 가능(웹사이트가 로딩되는 즉시 상호작용 가능)
- ### 단점

- 네크워크가 속도가 느린 환경에서는 번들이 모두 다운로드 될 때까지 빈 페이지를 보아야함

- 검색 로봇에게도 그 내용은 빈 것으로 보인다.

- 번들을 모두 받을 때까지 검색 로봇이 기다리기는 하지만 성능 점수는 낮다.

### 정적 사이트 생성(SSG)
- SSG는 일부 또는 전체 페이지를 빌드 시점에 미리 렌더링
장점

- 쉬운 확장 : 정적 페이지는 HTML 파일으므로 CDN을 통해 파일을 제공하거나, 캐시에 저장하기 쉬움

- 뛰어난 성능 : 빌드 시점에 미리 렌더링하기 때문에 페이지를 요청해도 클라이언트나 서버가 무언가를 처리할 필요가 없음

- 더안전한 API 요청 : 외부 API를 호출하거나, DB에 접근하거나, 보호해야 할 데이터에 접근할 일이 없다. 필요한 모든 정보가 빌드 시 포함되어 있기 때문이다.
## 9월 4일
### 바벨과 웹팩 설정 커스터마이징
- 바벨이나 웹팩의 설정도 커스터마이징 할 수 있습니다.
- 바벨은 자바스크립트 트랜스컴파일러이며, 최신 자바스크립트 코드를 하위 호환성을 보장하는 스크립트 코드로 변환하는 일을 담당합니다.
- 하위 호환성이 보장되면 어떤 웹 브라우저에서든 자바스크립트 코드를 실행할 수 있습니다.
- 바벨을 사용하면 브라우저나 Node.js 등에서 지원하지 않는 새롭고 훌륭한 기능을 현재의 환경에서도 실행할 수 있습니다.
- 바벨 설정을 커스터마이징 하려면, 프로젝트 Root에 .babelrc라는 파일을 생성하면 됩니다.
- 이 설정 파일을 비워 두면 오류가 발생하기 때문에 최소한 다음 내용을 저장해야 합니다.

### 타입스크립트 지원
- Next.js는 타입스크립트로 작성되었기 때문에 고품질의 type definiton을 지원합니다.
- 기본 언어를 타입스크립트로 지정하려면 root에 tscoonfig.json이라는 설정파일 생성하면 됩니다.
- 그런 다음 `npm run dev` 명령을 실행하면 됩니다.

### 프로젝트의 기본 구조
- pages/ 디렉토리 안의 모든 js파일은 public 페이지가 됩니다.
- pages/ 의 Index.jsv 파일을 복사해서, about.js로 이름을 바꾸면, localhost:3000/about으로 접속할 수 있습니다.
- public/ 디렉토리에는 웹 사이트의 모든 퍼블릭 페이지와 정적 콘텐츠가 있습니다.
- styles/ 디렉토리에는 앱에서 사용하는 스타일시트 넣습니다.
- 용도가 정해져 있는 디렉토리는 pages/ 와 public 뿐입니다.
- 나머지 디렉토리는 필요에 따라서 다른 목적으로 사용하거나 삭제해도 됩니다.

### 프로젝트 생성 방법
- create-next-app을 이용하여 프로젝트를 생성해봅니다.
- `npx create-next-app<app-name>` -> `npx create-next-app@latest`
- Next 13.4 부터 라우터가 /src/pages에서 /src/app로 정식 변경
- 만일 pages를 사용하고 싶다면, App Router를 No라고 해주면 됩니다.
- 프로젝트가 생성되면 프로젝트 디렉토리로 이동하여 다음 명령을 실행합니다. npm run dev
- React처럼 바로 실행되지 않기 때문에 localhost:3000으로 접속하여 확인합니다. "Ctrl + 클릭"

### nvm의 주요 명령어
- 설치되어 있는 node 리스트 확인
- Node 설치 : install
    - nvm install 16.16.0  -> 16.16.0 버전 설치
    - nvm install latest  -> latest current version 설치
    - nvm install lts
- Node 삭제 : uninstall
- 사용할 Node 버전 선택 : use
- 사용중인 버전 확인 : current
- 설치 가능한 node version 목록 확인 : list available

## 8월 28일
### Page Router vs App Router
- React로 개발하다 처음 Next를 사용하면 제일 먼저 놀라는 기능이 Router 입니다.
- Next.js 13.4에서부터 App Router가 Stable하게 지원하기 시작했습니다.

#### [Page Router]
- pages 디렉토리가 root이고, index.js가 index page가 됩니다.
- about.js는 /about , team.js는 /team 경로로 라우팅 됩니다.
- 클라이언트 중심의 라우팅입니다.

#### [App Router]
- app 디렉토리가 root이고, page.js가 index page가 됩니다.
- /about/page.js는 /about, /login/page.js는 /page 경로로 라우팅 됩니다.
- 서버 중심의 라우팅입니다.
- 번들 사이즈가 작습니다.

### Next.js 13 vs 14
- Pages Router -> App Router
- Data Fetching : 13까지는 getServerSideProps, getStaticProps 메소드를 이용해서 구현 했으나, 14에서는 SSG(정적 사이트 생성), SSR(서버측 렌더링) 및 ISR(증분적 정적 재생성)에서 하나의 API만을 사용해서 구현할 수 있게 되었습니다.
- Tubopack : Rust 기반으로 개발된 새로운 번들러 사용으로 webpack보다 700배 빠르다고 발표했습니다.
- Tubopack은 3000개의 모듈이 있는 애플리케이션에서 1.8초 만에 부팅되고, Webpack은 16.5초, Vite는 11.4초가 걸립니다.



- 이미지 최적화 : 13까지는 도구를 사용하였으나, 14부터는 자체적으로 지원하기 시작했습니다.
- 보안 강화 : XSS 공격에 대한 보호 기능이 강화되고, 보안 관련 헤더 설정을 더욱 쉽게 만들었습니다.

### Next.js 알아보기
- Next.js는 리액트를 위해 만든 오픈소스 자바스크립트 웹 프레임워크 입니다.
- 리액트에는 없는 다양한 기능을 제공합니다.
    - 서버 사이드 렌더링(SSR: Server Side Rendering)
    - 정적 사이트 생성(SSG: Static Site Generation)
    - 증분 정적 재생성(ISR: Incremental Static Regenration)

<br>

`SSG(정적 페이지 생성)는 미리 만들어 놓은 페이지를 서비스 하기 때문에 속도는 빠르지만, 한번 생성하고 나면 수정이 불가능합니다. 이러한 단점을 보완하고자 나온 것이 ISR(증분 정적 재생성)입니다. 이미 생성된 페이지를 일정 시간이 지난 후에 다시 생성합니다. (최신 데이터로 업데이트)`

### [Chapter 1의 주요 내용]
- Next.js 소개 / 다른 프레임워크와의 비교 / 리액트와의 차이점
- 기본구조 타입스크립트를 사용하는 방법
- 바벨과 웹팩 설정 커스터 마이징 (Next.js 14는 Webpack에서 Tubopack으로 바뀜)

### 준비하기
- Node.js와 npm을 설치하거나 codesandbox.io 혹은 repl.it 등의 사이트를 이용합니다.
- 이 후 프로젝트별로 필요한 의존성 패키지를 npm으로 설치합니다.
- 잠시 확인을 위한 것이면 사이트를 이용해도 되지만, 그렇지 않은 경우라면 local에 환경을 설정하는 것이 좋습니다.

### Next.js란?
Angular, React, Vue 와 같은 프레임워크가 등장하면서 웹 개발 분야는 급속도로 변하기 시작
1. 리액트는 메타(페이스북)의 조던 발케가 만들어 2013년 오픈소를 발표합니다.
2. 리액트는 클라이언트 사이드(CSR)에서만 작동한다는 문제점이 있습니다.
3. 이 문제를 해결하기 위해 서버에서 미리 렌더링해 두는 방법을 연구하기 시작합니다.
4. 이러한 연구의 결과로 나온 것이 Next.js입니다.
5. 이 밖에 리액트가 제공하지 않는 여러가지 기능을 제공하게 됩니다.

#### [Next.js가 제공하는 새로운 기능들]
- 코드 분할 : 페이지를 로딩 할 때 번들을 여러 조각으로 나누어 필요한 부분만 전송
- 파일 기반 라우팅
- 경로 기반 프리페칭 : 사용자가 다음에 이동할 수 있는 페이지를 미리 가져오는 기술
- 서버 사이드 렌더링 : 페이지 요청이 올때마다 사전 렌더링
- 정적 사이트 생성 : 빌드하는 동안 페이지를 사전 생성
- 증분 정적 콘테츠 생성 : 배포 후에도 재 배포 없이 계속 업데이트
- 타입스크립트 기본 지원
- 자동 폴리필 적용 : 이전 브라우저에서 최신 기능을 제공하는데 필요한 코드를 제공
- 이미지 최적화 : Next/image 컴포넌트 제공하는 이미지의 최적화 기술(lazy loading-지연, 이미지 사이즈 최적화- webp 변환, Placeholder-영역 확보)
- 웹 애플리케이션의 국제화 지원 : 다국어 지원(local에 맞는 URL로 라우팅)
- Next.js는 현재 넷플릭스, 트위치, 틱톡, 나이키 등 사이트에서 사용 중입니다.

### Next.js와 비슷한 프레임워크
#### [Gatsby]
- 정적 웹 사이트를 만들 수 있는 프레임워크 입니다.
- 정적 사이트 생성만 지원합니다.
- 클라이언트 사이드 렌더링만 지원합니다.
- 동적으로 변하는 복잡한 웹 사이트는 만들 수 없습니다.

#### [Razzle]
- 서버 사이드 렌더링이 가능한 자바스크립트 애플리케이션 개발이 가능합니다.
- CRA와 유사하게 프로젝트를 구성할 수 있다는 장점이 있습니다.
- React, Preact, Reason-React, Angular 및 Vue 와 함께 사용할 수 있습니다.

#### [Nuxt.js]
- Vue를 사용한 웹 애플리케이션 개발에서 리액트의 Next.js에 해당하는 프레임워크입니다.
- Nuxt.js나 Next.js 모두 같은 목표를 갖는 프레임워크지만 Nuxt.js는 더 많은 설정이 필요합니다.
- 만약 Vue 개발자가 서버 사이드 렌더링이 필요하다면 Nuxt.js를 사용해 보는 것도 추천합니다.

#### [Angular Universal]
- 정적 사이트 생성과 서버 사이드 렌더링을 지원합니다.
- Nuxt나 Next와는 달리 대기업인 구글에서 만들었습니다.
- Angular로 개발하는 경우 Angular Universal을 사용하는 것이 대부분입니다.

**2024**
![alt text](images/stackover.png)

### 왜 Next.js 일까?
- React에서 제공하지 않는 여러 기능을 지원합니다.
- 설정이나 개발 옵션 등 다양한 부분에서도 유용한 기능들을 제공합니다.

### 리액트에서 Next.js로
- Next.js의 기본 철학은 React와 거의 같습니다.
- 설정보다 관습 이라는 리택트의 철학을 계승합니다.
- CoC : Convention over Configuration은 개발자가 해야 할 결정의 수를 줄여주면서도, 유연성은 잃지 않도록 하는 소프트웨어 설계 패러다임입니다.
- 반면 fs, child_process와 같이 Node.js에서만 사용할 수 있는 라이브러리나 API를 사용하려는 경우, 각 요청 별 데이터를 클라이언트로 보내기 전에 서버 사이드 코드를 실행하거나 페이지 생성 시점에 해당 코드를 처리하는 방식을 지원합니다.
    - 어떤 방식으로 지원하는지는 각 페이지가 어떤 렌더링 방식을 사용하는가에 따라 결정됩니다.

- 클라이언트 사이드 앱, 프로그레시브 웹 앱, 오프라인 웹 앱도 쉽게 만들 수 있습니다.
- 많은 내장 컴포넌트와 최적화 기능을 사용할 수 있다는 장점이 있습니다.

### Next.js 시작하기
- 새로운 앱을 만들고 웹팩과 바벨의 기본 설정을 커스터마이징 해봅니다.
- Next.js 앱을 만들 때 기본 언어로 타입스크립트를 사용하는 방법을 알아 봅니다.
- Typescirpt는 이번학기 수강하기 때문에, 이 강의는 js로 진행합니다.
- 개발 환경에는 Node.js npm만 설치하면 됩니다.
- Node.js만 설치하면 npm은 함께 설치됩니다.
- 설치 되어 있는지 확인하려면 버전을 확인해 봅니다.
- 이번 학기에는 v22.7.0 버전으로 진행합니다.
- 만일 다른 버전을 이미 사용하고 있다면 그대로 사용해도 됩니다.
- 사용 중 의존성에 문제가 있다고 판단될 때 변경하면 됩니다.