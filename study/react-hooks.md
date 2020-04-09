## React Hooks

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
