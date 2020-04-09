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
    ```
    함수 이름(컴포넌트 이름)은 대문자로 시작되어야 하며, function 다음에 둔다.
    함수가 반환되는 것은 렌더링되는 HTML 요소로 React에서는 JSX라고 한다.
    마지막에는 export로 함수를 노출한다.

- 클래스 형태 형태 컴포넌트 정의
    - 꼭 필요할때 사용
    - 기본 코드 유지보수에 사용
     ```javascript
    import React, { Component } from 'react';

    class 
    function Hello() {
        return <div>안녕하세요.</div>;
    }

    export default Hello;

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

### 컴포넌트에게 값 전달 (props)
- 단어 props는 Properties의 줄임말
- 기능 props는 특정 값을 컴포넌트에 넘기고 싶을 때 사용
    ```javascript
    <Child value="value" />>
    ```
- 다음과 같이 Hello에 name, color 속성에 가각 'react', 'red' 라는 것을 넘기면,
    ```javascript
    function App() {
        return (
            <Hello name="react" color="read" />
        )
    }
    ```
    Hello 함수에 props라는 파라미터를 만들어서 사용함.
    ```javascript
    function Hello(props) {
        return (
            <div style={{
                color: props.color
            }}>안녕하세요, {props.name}!</div>
        )
    }
    ```
- Props를 받을 때, 구조 분해서 받으면 코드가 간단해짐
    ```javascript
    function Hello({color, name}) {
        return (
            <div style={{
                color: color
            }}>안녕하세요, {name}!</div>
        )
    }
    ```
- 스타일에서 속성명과 그 속성에 설정한 변수명이 같다면...
    ```javascript
    function Hello({color, name}) {
        return (
            <div style={{
                color
            }}>안녕하세요, {name}!</div>
        )
    }
    ```
- Props 기본값을 설정하고 싶을때
    ```javascript
    import React from 'react';

    function Hello({color, name}) {
        return (
            <div style={{
                color
            }}>안녕하세요, {name}!</div>
        )
    }

    Hello.defaultProps = {
        name: '이름없음'
    }

    export default Hello;
    ```
- Props Children이란? 속성이 아니라, 태그 값임.
    ```javascript
    <Wrapper>Children</Wrapper>
    ```
- Props Children은 다음과 같이 사용 함
    ```javascript
    function Wrapper({children}) {
        const style = {
            border: '2px solid black',
            padding: 16
        };
        return <div style={style}>{children}</div>
    }

    function App() {
        return (
            <Wrapper>
                <Hello name="react" color="read" />
                <Hello name="react" color="read" />
            </Wrapper>
        )
    }
    ```
### 조건부 렌더링
- 조건부 렌더링 예제 (true/false는 {} 묶어서 사용해야 함)

    ```javascript    
    function App() {
        return (
            <Wrapper>
                <Hello color="read" isSpecial={true}/>
                <Hello name="react" color="read" />
            </Wrapper>
        )
    }
    ```
    ```javascript
    import React from 'react';

    function Hello({color, name, isSpecial}) {
        return (
            <div style={{
                color
            }}>
                {isSpecial ? <b>*</b> : null}
                안녕하세요, {name}!
            </div>
        )
    }

    Hello.defaultProps = {
        name: '이름없음'
    }

    export default Hello;
    ```
    JSX에서는 null, false, undefined를 렌더링하면 아무것도 표시하지 않음

    어떤 조건일때 무엇인가 표시만 하는 루팅이 있다면 다음과 같이 &&를 사용해도 됨.
    ```javascript
    import React from 'react';

    function Hello({color, name, isSpecial}) {
        return (
            <div style={{
                color
            }}>
                {isSpecial && <b>*</b>}
                안녕하세요, {name}!
            </div>
        )
    }

    Hello.defaultProps = {
        name: '이름없음'
    }

    export default Hello;
    ```

    또, 부울린 값을 전달때 true이면 다음과 같이 줄여서 사용할 수 있음.
    ```javascript    
    function App() {
        return (
            <Wrapper>
                <Hello color="read" isSpecial/>
                <Hello name="react" color="read" />
            </Wrapper>
        )
    }
    ```
### 사용자 인터렉션에 따른 상태 관리 (useState : 함수형 컴포넌트 상태 관리)

- 이전 React 버전에는 함수형 컴포넌트에서는 상태 관리를 사용할 수 없었지만, 16.8 부터는 사용 가능해 졌음. 상태 관리 기능을 사용하기 위해서는 'react'에서 useState를 임포트하고, 반환값이 변수명, 설명 함수명을 배열로 주는 초기화 값을 포함한 useState를 호출하면 됨. (onClick)
    ```javascript    
    import React, {useState} from 'react';
    
    function Counter() {
        const [number, setNumber] = useState(0);

        const onIncrease = () => {
            setNumber(number + 1);
        }
        const onDecrease = () => {
            setNumber(number - 1);
        }
        return (
            <div>
                <h1>0</h1>
                <button onClick="{onIncrease}">+1</button>
                <button onClick="{onDecrease}">-1</button>
            </div>
        )
    }
    ```    
    위 코드에서 주의해야할 것이 있다면 이벤트를 연결할때 다음 처럼 호출형태로 코딩하면 안됨. (랜더링 될때 호출되기 때문)
    ```javascript    
    function Counter() {
        return (
            <div>
                <h1>{number}</h1>
                <button onClick="{onIncrease()}">+1</button>
                <button onClick="{onDecrease()}">-1</button>
            </div>
        )
    }
    ```
    다음 처럼 useState의 설정 함수는 과거값을 참조하여 어떻게 업데이트 할지 설정할 수 있음. (함수형 업데이트, 최적화와 관련 있음)
    ```javascript    
    const onIncrease = () => {
        setNumber(prevNumber => prevNumber + 1);
    }
    ```
- 입력값 상태 관리 (onChange)
    ```javascript
    import React, {useState} from 'react';

    function InputSample() {
        const [text, setText] = useState('');

        const onChange = (e) => {
            setText(e.target.value);
        }

        const onReset = () => {
            setText('');
        }
        return (
            <input onChange={onChange} value={text}/>
            <button onClick={onReset}>초기화</button>
            <div>
                <b>값: </b>
                {text}
            </div>
        );
    }

    export default InputSample;
    ```
- 여러개의 입력값 상태 관리 (여러개의 useState과 onChange를 만들면 효과적이지 않음, 객체를 설정할때는 spread '...'을 사용해서 복사한 후 변화된 값들을 수정함)
     ```javascript
    import React, {useState} from 'react';

    function InputSample() {
        const [inputs, setInputs] = useState({
            name: '',
            nickname: '',
        });
        const {name, nickname} = inputs;
        const onChange = (e) => {
            const { name, value } = e.target;

            setInputs({
                ...inputs,
                [name]: value,
            });
        }

        const onReset = () => {
            setInputs({
                name: '',
                nickname: '',
            });
        }
        return (
            <input name="name" placeholder="이름" 
                onChange={onChange}
                value={name}/>
            <input name="nickname" placeholder="닉네임"
                onChange={onChange}
                value={nickname}/>
            <button onClick={onReset}>초기화</button>
            <div>
                <b>값: </b>
                {name} ({nickname})
            </div>
        );
    }

    export default InputSample;
    ```

### 특정 DOM 선택하기 (함수형에서 useRef) 

- 특정 DOM 크기, Scroll 위치, Focus 등에 특정 상황에서 DOM 내용을 가져와야 할때 사용
    ```javascript
    import React, {useState, useRef} from 'react';

    function InputSample() {
        const [inputs, setInputs] = useState({
            name: '',
            nickname: '',
        });
        const nameInput = useRef();
        const {name, nickname} = inputs;
        const onChange = (e) => {
            const { name, value } = e.target;

            setInputs({
                ...inputs,
                [name]: value,
            });
        }

        const onReset = () => {
            setInputs({
                name: '',
                nickname: '',
            });
            nameInput.current.focus();
        }
        return (
            <input name="name" placeholder="이름" 
                onChange={onChange}
                value={name}
                ref={nameInput}/>
            <input name="nickname" placeholder="닉네임"
                onChange={onChange}
                value={nickname}/>
            <button onClick={onReset}>초기화</button>
            <div>
                <b>값: </b>
                {name} ({nickname})
            </div>
        );
    }

    export default InputSample;
    ```
    위 코드에서 useRef 함수를 정의하고, JSX에서 접근하고자 하는 DOM의 속성에 ref를 설정하고, current를 사용하여서 DOM 함수를 사용하면 됨.
- 렌더링과 상관없은 변수 사용에도 useRef를 이용할때도 있음

### [배열 렌더링하기](https://github.com/flyingmt/flyingmt.github.io/blob/master/study/react-array-rendering.md)

### 함수형 컴포넌트의 useEffect Hook

- 컴포넌트가 나타날때(Mount)와 사라질때(Unmount) 추가 작업을 할때 사용
- useEffect 사용시 두번째 파라미터는 의존되는 값들임

    ```javascript
    import React, {useEffect} from 'react';        

    function User({ user, onRemove, onToggle }) {
        const {username, email, id, active } = user;
        useEffect(() => {
            console.log('컴포넌트가 화면에 나타남');
            return () => {
                console.log('컴포넌트가 화면에 사라짐');
            }
        }, []);
        return (
            <div>
                <b 
                    onClick={() => onToggle(id)}
                    style={{
                    color: active ? 'green' : 'black',
                    cursor: 'pointer'
                }}>&nbsp;{username}</b> <span>({email})</span>
                <button onClick={() => onRemove(id)}>삭제</button>
            </div>
        );
    }

    function UserList({users, onRemove, onToggle}) {
        return (
            {
                users.map(
                    (user, index) => (
                        <User 
                            user={user} 
                            key={user.id} 
                            onRemove={onRemove}
                            onToggle={onToggle} />
                    )
                )
            }
        );
    }

    export default UserList;
    ```
- Mount 될때 주로 추가하는 작업들
    - props로 받은 것을 state로 설정할때
    - 외부 API 요청시
    - 라이브러리(D3, Video.js 등) 초기화할때
    - setInterval, setTimeout를 사용할때

- Unmount 될때 주로 추가하는 작업들
    - clearInterval, clearTimeout를 사용할때
    - 라이브러리 인스턴스 제거

- 속성 user가 변경되었을때 마다 호출되는 예제

    ```javascript
    import React, {useEffect} from 'react';        

    function User({ user, onRemove, onToggle }) {
        const {username, email, id, active } = user;
        useEffect(() => {
            console.log(user);
            return () => {
                console.log(user);
            }
        }, [user]);
        return (
            <div>
                <b 
                    onClick={() => onToggle(id)}
                    style={{
                    color: active ? 'green' : 'black',
                    cursor: 'pointer'
                }}>&nbsp;{username}</b> <span>({email})</span>
                <button onClick={() => onRemove(id)}>삭제</button>
            </div>
        );
    }
    ```

### 함수형 컴포넌트의 useMemo Hook (변수 최적화)

- 이전 연산된 값을 재사용할때 사용
- 성능 최적화할때 사용

    ```javascript
    import React, {useRef, useState, useMemo} from 'react';

    function countActiveUsers(users) {
        console.log('활설 사용수를 세는중...');
        return users.filter(user => user.active).length;
    }
    
    const count = userMemo(() => countActiveUsers(users), [users]);

    return (
            <>
                <CreateUser 
                    username={username}
                    email={email}
                    onChange={onChange}
                    onCreate={onCreate} />
                <UserList users={users} />
                <div>활성 사용자 수 : {count}</div>
            </>
        );
    ```

### 함수형 컴포넌트의 useCallback Hook (함수 최적화)

- 이전에 만들었는 함수를 새로 만들지 않고 재활용할 때 사용

    ```javascript
    import React, {useRef, useState, useMemo, useCallback} from 'react';

    function countActiveUsers(users) {
        console.log('활설 사용수를 세는중...');
        return users.filter(user => user.active).length;
    }

    const onChange = useCallback(e => {
            const {name, value} = e.target;
            setInputs({
                ...inputs,
                [name]: value
            });
        }, [inputs]);

    const onCreate = useCallback(() => {
            const user = {
                id: nextId.current,
                username,
                email,
            };
            //setUsers([...users, user]);
            serUsers(users.concat(user));
            setInputs({
                username: '',
                email: '',
            });
            nextId.current += 1;
        }, [username, email, users]);

    const onRemove = useCallback(id => {
        setUsers(users.filter(user => user.id !== id));
    }, [users]);

    const onToggle = useCallback(id => {
        setUsers(users.map(
            user => user.id === id
                ? {...user, active: !user.active}
                : user
        ))
    });
    
    const count = useMemo(() => countActiveUsers(users), [users]);

    return (
            <>
                <CreateUser 
                    username={username}
                    email={email}
                    onChange={onChange}
                    onCreate={onCreate} />
                <UserList users={users} />
                <div>활성 사용자 수 : {count}</div>
            </>
        );
    ```

### 함수형 컴포넌트의 React.memo (컴포넌트 최적화)

- 렌더링 성능 최적화할때 사용됨 (함수형 업데이트 수정이 필요)

    ```javascript
    import React from 'react';        

    function CreateUser({ username, email, onChange, onCreate }) {
        return (
            <div>
                <input 
                    name="username" 
                    onChange={onChange}
                    value={username}/>
                <input
                    name="email" 
                    onChange={onChange}
                    value={email}/>
                <button onClick={onCreate}>등록</button>
            </div>
        );
    }

    export default React.memo(CreateUser);
    ```
    ```javascript
    import React, {useEffect} from 'react';        

    const User = React.memo(function User({ user, onRemove, onToggle }) {
        const {username, email, id, active } = user;
        useEffect(() => {
            console.log('컴포넌트가 화면에 나타남');
            return () => {
                console.log('컴포넌트가 화면에 사라짐');
            }
        }, []);
        return (
            <div>
                <b 
                    onClick={() => onToggle(id)}
                    style={{
                    color: active ? 'green' : 'black',
                    cursor: 'pointer'
                }}>&nbsp;{username}</b> <span>({email})</span>
                <button onClick={() => onRemove(id)}>삭제</button>
            </div>
        );
    });

    function UserList({users, onRemove, onToggle}) {
        return (
            {
                users.map(
                    (user, index) => (
                        <User 
                            user={user} 
                            key={user.id} 
                            onRemove={onRemove}
                            onToggle={onToggle} />
                    )
                )
            }
        );
    }

    export default React.memo(
        UserList,
        (preProps, nextProps) => nextProps === preProps.user);
    ```
    ```javascript
    import React, {useRef, useState, useMemo, useCallback} from 'react';

    function countActiveUsers(users) {
        console.log('활설 사용수를 세는중...');
        return users.filter(user => user.active).length;
    }

    const onChange = useCallback(e => {
            const {name, value} = e.target;
            setInputs({
                ...inputs,
                [name]: value
            });
        }, [inputs]);

    const onCreate = useCallback(() => {
            const user = {
                id: nextId.current,
                username,
                email,
            };
            //setUsers([...users, user]);
            serUsers(users => users.concat(user));
            setInputs({
                username: '',
                email: '',
            });
            nextId.current += 1;
        }, [username, email]);

    const onRemove = useCallback(id => {
        setUsers(users => users.filter(user => user.id !== id));
    }, []);

    const onToggle = useCallback(id => {
        setUsers(users => users.map(
            user => user.id === id
                ? {...user, active: !user.active}
                : user
        ))
    }, []);
    
    const count = useMemo(() => countActiveUsers(users), [users]);

    return (
            <>
                <CreateUser 
                    username={username}
                    email={email}
                    onChange={onChange}
                    onCreate={onCreate} />
                <UserList users={users} />
                <div>활성 사용자 수 : {count}</div>
            </>
        );
    ```

### 함수형 컴포넌트의 useReducer Hook 

- useState 대신에 상태를 업데이트할 수 있음
- useState은 값 기반으로 업데이트하며, useReducer는 액션 기반으로 업데이트함.
- 상태 업데이트 로직을 컴포넌트 밖으로 분리 가능

    ```javascript    
    import React, {useReducer} from 'react';

    function reducer(state, action) {
        switch(action.type) {
            case 'INCREMENT':
                return state + 1;
            case 'DECREMENT':
                return state - 1;
            default:
                //return state;
                throw new Error('Unhandled action');
        }
    }
    
    function Counter() {
        const [number, dispatch] = useReducer(reducer, 0);

        const onIncrease = () => {
            dispatch({
                type: 'INCREMENT'
            });
        }
        const onDecrease = () => {
            dispatch({
                type: 'DECREMENT'
            });
        }
        return (
            <div>
                <h1>0</h1>
                <button onClick="{onIncrease}">+1</button>
                <button onClick="{onDecrease}">-1</button>
            </div>
        )
    }
    ```
- 사용시 초기값을 설정해야 함

    ```javascript
    import React, {useRef, useReducer, useMemo, useCallback} from 'react';

    function countActiveUsers(users) {
        console.log('활설 사용수를 세는중...');
        return users.filter(user => user.active).length;
    }

    function reducer(state, action) {
        switch (action.type) {
            case 'CHANGE_INPUT':
                return {
                    ...state,
                    inputs: {
                        ...state.inputs,
                        [action.name] : action.value
                    }
                };
            case 'CREATE_USER':
                return {
                    inputs: initialState.inputs,
                    users: state.users.concat(action.user)
                };
            case 'TOGGLE_USER':
                return {
                    ...state,
                    users: state.users.map(user => 
                        user.id === action.id
                            ? {...user, active: !user.active}
                            : user
                        )
                };
            case 'REMOVE_USER':
                return {
                    ...state,
                    users: state.users.filter(user => user.id !== action.id)
                }
            default:
                throw new Error('Unhandled action');
        }
    }

    function App() {
        const [state, dispatch] = useReducer(reducer, initialState);
        const nextId = useRef(4);
        const { users } = state;
        const { username, email } = state.inputs;

        const onChange = useCallback(e => {
            const { name, value } = e.target;
            dispatch({
                type: 'CHANGE_INPUT',
                name,
                value
            })
        }, []);

        const onCreate = useCallback(() => {
            dispatch({
                type: 'CREATE_USER',
                user: {
                    id: nextId.current,
                    username,
                    email,
                }
            });
            nextId.current += 1;
        }, [username, email])

        const onToggle = userCallback(id => {
            dispatch({
                type: 'TOGGLE_USER',
                id
            });
        }. []);

        const onRemove = userCallback(id => {
            dispatch({
                type: 'REMOVE_USER',
                id
            });
        }. []);

        const count = userMemo(() => countActiveUsers(users), [users]);

        return (
            <>
                <CreateUser 
                    username={username}
                    email={email}
                    onChange={onChange}
                    onCreate={onCreate} />
                <UserList 
                    users={users}
                    onToggle={onToggle}
                    onRemove={onRemove} />
                <div>활성 사용자 수 : {count}</div>
            </>
        );
    }    
    ```
- 언제 사용하는가? useReducer vs useState
    - 하나의 값이면 useState를 사용 (간단한것들)
    - 여러개의 값이거나 구조가 복잡하면 useReducer를 사용 (복잡한것들)

### 함수형 컴포넌트의 Custom Hook

- 반복된 코드에서 유용함 (예: 입력값 변경 처리)
- 함수는 use로 시작함.
- useState 대신에 useReducer를 사용해도 됨.
    ```javascript
    import { useState, useCallback } from 'react';

    function useInputs(initialForm) {
        const [form, setForm] = useState(initialForm);
        const onChange = useCallback(e => {
            const {name, value} = e.target;
            setForm(form => ({...form, [name]: value}));
        }, []);
        const reset = useCallback(() => setForm(initialForm), [initialForm]);

        return [form, onChange, reset];
    };

    export default useInputs;
    ```
    ```javascript
    import React, {useRef, useReducer, useMemo, useCallback} from 'react';

    function countActiveUsers(users) {
        console.log('활설 사용수를 세는중...');
        return users.filter(user => user.active).length;
    }

    function reducer(state, action) {
        switch (action.type) {
            case 'CREATE_USER':
                return {
                    users: state.users.concat(action.user)
                };
            case 'TOGGLE_USER':
                return {
                    ...state,
                    users: state.users.map(user => 
                        user.id === action.id
                            ? {...user, active: !user.active}
                            : user
                        )
                };
            case 'REMOVE_USER':
                return {
                    ...state,
                    users: state.users.filter(user => user.id !== action.id)
                }
            default:
                throw new Error('Unhandled action');
        }
    }

    function App() {
        const [state, dispatch] = useReducer(reducer, initialState);
        const [form, onChange, reset] = useInputs({
            username: '',
            email: '',
        });
        const { username, email } = form;
        const nextId = useRef(4);
        const { users } = state;

        const onCreate = useCallback(() => {
            dispatch({
                type: 'CREATE_USER',
                user: {
                    id: nextId.current,
                    username,
                    email,
                }
            });
            nextId.current += 1;
            reset();
        }, [username, email, reset])

        const onToggle = userCallback(id => {
            dispatch({
                type: 'TOGGLE_USER',
                id
            });
        }. []);

        const onRemove = userCallback(id => {
            dispatch({
                type: 'REMOVE_USER',
                id
            });
        }. []);

        const count = userMemo(() => countActiveUsers(users), [users]);

        return (
            <>
                <CreateUser 
                    username={username}
                    email={email}
                    onChange={onChange}
                    onCreate={onCreate} />
                <UserList 
                    users={users}
                    onToggle={onToggle}
                    onRemove={onRemove} />
                <div>활성 사용자 수 : {count}</div>
            </>
        );
    }    
    ```

### 전역값 관리 (Context API)

- 이벤트 함수를 전달 할때 여러 단계를 걸쳐서 전달해야 할때 여러 단계를 생략할 수 있음.
    ```javascript
    import React, { createContext, useContext } from 'react';

    const MyContext = createContext('defaultValue');

    function Child() {
        const text = useContext(MyContext);
        return <div>안녕하세요? {text}</div>
    }

    function Parent() {
        return <Child  />
    }

    function GrandParent() {
        return <Parent />
    }

    function ContextSample() {
        return (
            <MyContext.Provider value="GOOD">
                <GrandParent />
            </MyContext.Provider>
        )
    }

    export ContextSample;
    ```
- Context는 유동적으로 설정할 수 있음.
    ```javascript
    import React, { createContext, useContext, useState } from 'react';

    const MyContext = createContext('defaultValue');

    function Child() {
        const text = useContext(MyContext);
        return <div>안녕하세요? {text}</div>
    }

    function Parent() {
        return <Child  />
    }

    function GrandParent() {
        return <Parent />
    }

    function ContextSample() {
        const [value, setValue] = useState(true);
        return (
            <MyContext.Provider value="{value ? 'GOOD' : 'BAD'">
                <GrandParent />
                <button onClick={() setValue(!value)}>Click Me</button>
            </MyContext.Provider>
        )
    }

    export ContextSample;
    ```
- 전역으로 설정해야 함
    ```javascript
    export const UserDispatch = createContext(null);
    ```
### immer 라이브러리로 불변성 쉽게 하기 (꼭 필요할때 사용 - 성능이 약간 낮음)

- immer를 사용하면 불변성을 해치는 코드를 작성해도 대신 불변성 유지를 해줌.
- 다음과 같이 추가함.
    ```
    $ yarn add immer
    ```
- 그리고 다음과 같이 사용함.    
    ```javascript
    import React, {useRef, useReducer, useMemo, useCallback} from 'react';
    import produce from 'immer';

    function reducer(state, action) {
        switch (action.type) {
            case 'CREATE_USER':
                return produce(state, draft => {
                    draft.users.push(action.user);
                });
            case 'TOGGLE_USER':
                return produce(state, draft => {
                    const user = draft.users.find(user => user.id === action.id);
                    user.active = !user.active;
                });
            case 'REMOVE_USER':
                return produce(state, draft => {
                    const index = draft.users.findIndex(user => user.id === action.id);
                    draft.users.splice(index. 1);
                });
            default:
                throw new Error('Unhandled action');
        }
    }
    ```
