## React

### 왜 React를 사용하는가?

- Virtual DOM을 사용하여 화면 업데이트 속도가 빠르고, 코드 유지보수가 쉽다.
- UI를 어떻게 보여줄지 코딩을 집중할 수 있음
- 컴포넌트(UI 작은 조각) 별로 개발을 하여 쉬고 빠르게 개발이 됨

### 작업 환경 구성하기

- 온라인 작업 환경을 원한다면 [Code Sandbox](https://codesandbox.io/)을 사용.
- 아니면 다음을 사용함
    * [NodeJS](https://nodejs.org/ko/) (자바스크립트 런타임)
    * [Yarn](https://classic.yarnpkg.com/en/docs/install/#windows-stable) (자바스크립트 패키지 관리자 NPM 대체 - 더 빠름)
    * VS Code(에디터)
    * Git(형상관리 및 Bash 커맨드 사용 - Terminus도 관찮음)
- NodeJS 버전 관리를 위해서는 nvm 설치 사용함
- 중요 프로그램 설치는 다음과 같이 확인 함
    ```
    $ node --version
    $ yarn --version
    ```
- 새로운 React 프로젝트 만들기 (프로젝트 만들고자하는 폴더에서)
    ```
    $ npx create-react-app begin-react
    ```
- 프로젝트 실행 (개발 서버 실행, 수정시 자동 반영됨)
    ```
    $ cd begin-react
    $ yarn start
    ```
### 컴포넌트 만들기

- 항상 JS 파일 위에 React 라이브러리를 추가함
    ```javascript
    import React from 'react';
    ```
- 컴포넌트를 만들때는 함수형과 클래스형으로 만들수 있는 방법이 2가지가 있음
- 함수 형태 컴포넌트 정의 (Hello.js)
    ```javascript
    import React from 'react';

    function Hello() {
        return <div>안녕하세요.</div>;
    }

    export default Hello;
    ```
    함수 이름(컴포넌트 이름)은 대문자로 시작되어야 하며, function 다음에 둔다.
    함수가 반환되는 것은 렌더링되는 HTML 요소로 React에서는 JSX라고 한다.
    마지막에는 export로 함수를 노출한다.

- 컴포넌트 형태 컴포넌트 정의 (TODO)
- 컴포넌트 사용하기 (App.js)
    ```javascript
    import React from 'react';
    import Hello from './Hello';

    function App() {
        return (
            <div><Hello /></div>
        );
    }

    export default App;
    ```
- 어떻게 App이 브라우저에 표시되는가?
    - 파일 index.js에 다음 줄에 의해서 Id가 'root'인것에 표시함.
        ```javascript
        ReactDOM.render(<App />, document.getElementById('root));
        ```
    - 그럼 'root'은 어디에 있는가? 폴더 public에 index.html에 정의되어 있음
        ```javascript
        <div id='root'></div>
        ```
### 컴포넌트(JSX) 만들때 기본 규칙

- JSX은 BABEL 같은 툴을 퉁해서 Javascript로 변환되는 것음
- 태그는 꼭 닫혀있어야 함 (Self Closing Tag 활용)
    ```javascript
    <input />
    ```
- 여러개의 태그는 하나의 태그로 감싸져 있어야 함 (Fragment 활용 : 변환 될때 추가 태그가 붙여지지 않음)
    ```javascript
    <>
        <Hello />
        <Hello />
    </>
    ```
- JSX 내에서 Javascript 사용하기 (중괄호 활용)
    ```javascript
    const name = '출력되어야 하는것';
    <div>{name}</div>
    ```
- Style 사용 (따로 변수 사용하고, 속성들은 - 형태로 되어 있는 것들을 Camel 형태로 변경해줘야 함, 숫자는 기본이 Pixel이며 다른 단위를 사용하려면 문자열로 추가함)
    ```javascript
    const style = {
        backgroundColor: 'black',
        color: 'aqua',
        fontSize: 24,
        padding: '1rem'
    }

    return (
        <>
            <div style={style}>{name}</div>
        </>
    )
    ```
- Class Name 사용 (스타일 파일을 추가하고, class를 className로 고쳐서 사용함)
    ```javascript
    import App.css
        
    return (
        <>
            <div className="grey-box">{name}</div>
        </>
    )
    ```
- 주석은 {/* */} 혹은 // 을 사용한다.
    ```javascript
    return (
        <>
            {/* 주석입니다. */}
            <div 
                // 주석입니다.
                className="grey-box">{name}
            </div>
        </>
    )
    ```
