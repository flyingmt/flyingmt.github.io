## 클래스형 컴포넌트 (과거 유물...)

### 클래스 형태 컴포넌트 정의
- 꼭 필요할때 사용
- 기본 코드 유지보수에 사용
     ```javascript
    import React, { Component } from 'react';

    class Hello extends Component {
        static defaultProps = {
            name: '이름없음',
        }

        render() {
            const { color, isSpecial, name } = this.props;
            return (
                <div>안녕하세요.</div>
            );
        }
    }

    export default Hello;

### 사용자 인터렉션에 따른 상태 관리 (state와 setState : 클래스형 컴포넌트 상태 관리)

- 클래스형은 사용자 정의 함수를 만들어 이벤트에 붙여줌.
- 이때 함수에서 this을 사용할때 문제가 되는 것을 알아야함. (bind 해야함.)

    ```javascript
    import React, {Component} from 'react'

    class Counter extends Component {
        
        constructor(props) {
            super(props);
            this.handleIncrease = this.handleIncrease.bind(this);
            this.handleDecrease = this.handleDecrease.bind(this);
        }

        handleIncrease() {
            console.log('increase');
        }

        handleDecrease() {
            console.log('decrease');
        }

        render() {
            return (
                <div>
                    <h1>0</h1>
                    <button onClick={this.handleIncrease}>+1</button>
                    <button onClick={this.handleDecrease}>-1</button>
                </div>
            );
        }
    }

    export default Counter;
    ```

- 커스텀 함수 매핑을 통해서도 bind를 할 수 있음.

    ```javascript
    import React, {Component} from 'react'

    class Counter extends Component {

        handleIncrease = () => {
            console.log('increase');
        }

        handleDecrease = () => {
            console.log('decrease');
        }

        render() {
            return (
                <div>
                    <h1>0</h1>
                    <button onClick={this.handleIncrease}>+1</button>
                    <button onClick={this.handleDecrease}>-1</button>
                </div>
            );
        }
    }

    export default Counter;
    ```

- 상태 관리를 위해서는 다음과 같이 함. (생성자, state은 무조건 객체이어야 함)
- setState을 할때 업데이트된 객체만 설정하면 됨. (객체 안에 객체는 모두다 업데이트 해야 함)

    ```javascript
    import React, {Component} from 'react'

    class Counter extends Component {
        //constructor(props) {
        //    super(props);
        //    this.state = {
        //        counter: 0
        //    };
        //}

        state = {
            counter: 0
        }

        handleIncrease = () => {
            this.setState({
                counter: this.state.counter + 1
            });
        }

        handleDecrease = () => {
            this.setState({
                counter: this.state.counter - 1
            });
        }

        render() {
            return (
                <div>
                    <h1>{this.state.counter}</h1>
                    <button onClick={this.handleIncrease}>+1</button>
                    <button onClick={this.handleDecrease}>-1</button>
                </div>
            );
        }
    }

    export default Counter;
    ```
- setState를 함수형 업데이트 형태로 사용 가능함 (바로 바로 적용)

     ```javascript
    import React, {Component} from 'react'

    class Counter extends Component {
        state = {
            counter: 0
        }

        handleIncrease = () => {
            this.setState(state => ({
                counter: this.state.counter + 1
            }));
        }

        handleDecrease = () => {
            this.setState({
                counter: this.state.counter - 1
            });
        }

        render() {
            return (
                <div>
                    <h1>{this.state.counter}</h1>
                    <button onClick={this.handleIncrease}>+1</button>
                    <button onClick={this.handleDecrease}>-1</button>
                </div>
            );
        }
    }

    export default Counter;
    ```
### 컴포넌트 생명주기(LifeCycle) 메서드 (클래스형에서만 작동)

- 함수들
    - constructor (생성될때)
        ```javascript
        constructor(props) {
            super(props);
            console.log("constructor");
        }
        ```
    - getDerivedStateFromProps (constructor, new Props, setState, forceUpdate)
        ```javascript
        static getDerivedStateFromProps(nextProps, prevState) {
            console.log("getDerivedStateFromProps");
            if (nextProps.color !== prevState.color) {
                return {color: nextProps.color };
            }
            return null;
        }
        ```
    - shouldComponentUpdate (true를 반환하면 렌더링됨)
        ```javascript
        shouldComponentUpdate(nextProps, nextState) {
            console.log('shouldComponentUpdate');
            return nextState.number % 10 !== 4;
        }
        ```
    - render
    - getShapshotBeforeUpdate (DOM 접근 가능)
        ```javascript
        getShapshotBeforeUpdate(prevProps, prevState) {
            console.log('getShapshotBeforeUpdate');
            if (prevProps.color !== this.props.color) {
                return this.myRef.style.color;
            }
            return null;
        }
        ```
    - componentDidMount (DOM 접근 가능)
        ```javascript
        componentDidMount() {
            console.log('컴포넌트가 보여지고 있습니다.');
        }
        ```
    - componentDidUpdate
        ```javascript
        componentDidUpdate(prevProps, prevState, snapshot) {
            console.log('componentDidUpdate');
        }
        ```
    - componentWillUnmount (제거될때)

- 마운트 순서
    - constructor
    - getDerivedStateFromProps
    - render
    - componentDidMount
- 업데이트 순서
    - getDerivedStateFromProps
    - shouldComponentUpdate
    - render
    - getShapshotBeforeUpdate
    - componentDidUpdate
- 언마운터 순서
    - componentWillUnmount

- 컴포넌트에서 에러 잡기 
    - componentDidCatch 메서드 사용함
        ```javascript
        import React, { Component } from 'react';

        class ErrorBoundary extends Component {
            state = {
                error: false
            };

            componentDidCatch(error, info) {
                console.log('에러가 발생');
                console.log({
                    error, info
                });
                this.setState({
                    error: true,
                })
            }

            render() {
                if(this.state.error) {
                    return <h1>에러 발생!</h1>
                }
                return this.props.children;
            }
        }

        export default ErrorBoundary;
        ```
    - Sentry에 에러 보내기
        ```
        yarn add @sentry/browser
        ```

        ```javascript
        import React, { Component } from 'react';
        import * as Sentry from '@sentry/browser';

        class ErrorBoundary extends Component {
            state = {
                error: false
            };

            componentDidCatch(error, info) {
                console.log('에러가 발생');
                console.log({
                    error, info
                });
                this.setState({
                    error: true,
                })

                if (process.env.NODE_ENV == 'production') {
                    Sentry.captureException(error, { extra: info });
                }
            }

            render() {
                if(this.state.error) {
                    return <h1>에러 발생!</h1>
                }
                return this.props.children;
            }
        }

        export default ErrorBoundary;
        ```

