## React Babel Webpack

- 프로젝트 만들기
    - npm init -y
    - .gitignore
        ```
        node_modules
        .DS_Store
        dist
        ```
- React
    - 관련 패키지 설치
        - yarn add react react-dom
    - 기본 파일 만들
        - mkdir app
        - index.css
        - index.js
        ```javascript
        var React = require('react');
        var ReactDOM = require('react-dom');
        require('index.css');

        class App extends React.Component {
        render() {
            return (<div></div>);
            }
        }

        ReactDOM.render(<App/>, document.getElementById('app'));
        ```
- WebPack & Babal
    - 관련 패키지 설치
        - yarn add --dev @babel/core @babel/preset-env @babel/preset-react webpack webpack-cli webpack-dev-server babel-loader css-loader style-loader html-webpack-plugin
    - webpack.config.js
    ```javascript
    // 모든 모듈들을 하나의 파일로 함쳐줌
    var path = require('path');
    var HtmlWebpackPlugin = require('html-webpack-plugin');

    module.export = {
        // 시작 파일 정의
        entry: './app/index.js', 
        // 출력 파일 정의
        output: { 
            // 현재 실행 경로
            path: path.resolve(__dirname, 'dist'), 
            // 파일명
            filename: 'index_bundle.js' 
        },
        // 출력전 코드 변환 방법 정의
        module: { 
            rules: [
                // 모든 JS 파일들에 대해서는 
                // babel-loader를 실행함
                { test: /\.(js)$/, use: 'babel-loader' },
                // 모든 CSS 파일들에 대해서는
                // style-loader와 css-loader를 실행함
                // style-loader: 모든 style를 페이지에 삽입함
                { test: /\.(css)$/, use: [ 'sytle-loader', 'css-loader' ]},
            ]
        },
        mode: 'development',
        plugins: [
            // 자동으로 index_bundle.js 파일을 사용하는 
            // index.html 설정
            new HtmlWebpackPlugin({
                template: 'app/index.html'
            })
        ]
    };
    ```        
    - app/index.html 파일만 들기
    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title>Github Battle</title>
    </head>
    <body>
        <div id="app"></div>
    </body>
    </html>
    ```
    
    - .babelrc 파일 만들기 (혹은 package.json에 "babel" 속성으로)
    ```
    {
        "presets": [
            "@babel/preset-env",    // Javascript 문법 변환
            "@babel/preset-react",  // React 문법 변환
        ]
    }
    ```

    - package.json에서
    ```
    "scripts": {
        "create": "webpack",
        "start": "webpack-dev-server --open"
    }
    ```