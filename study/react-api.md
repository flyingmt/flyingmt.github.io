## API 연동

### 간단한 API 연동 (axios 라이브러리)

- 설치 방법
    ```
    $ yarn add axios
    ```

- 다음과 같이 사용함 (promise 반환)
    ```javascript
    import axios from 'axios'

    axios.get('/user/1');

    axios.post('/users', {
        name: "ddddd"
    });
    ```

### 컴포턴트에서 데이터 로딩하기
- API 사용시 다음 3가지를 상태를 관리해야 함
    - 요청의결과
    - 로딩 상태
    - 에러

- useState & useEffect를 이용하면 다음과 같음

    ```javascript
    import React, {useState, useEffect} from 'react';
    import axios from 'axios';

    function Users() {
        const [users, setUsers] = useState(null);
        const [loading, setLoading] = useState(false);
        const [error, setError] = useState(null);

        const fetchUsers = async () => {
            try {
                setUsers(null);
                setError(null);
                setLoading(true);
                const response = await axios.get(
                    'https://jsonplaceholder.typicode.com/users/');
                setUsers(response.data);
            } catch (e) {
                setError(e);
            }
            setLoading(false);
        };

        useEffect(() => {        
            fetchUsers();
        }, []);

        if (loading) return <div>로딩중...</div>
        if (error) return <div>에러가 발생했습니다.</div>
        if (!users) return null;

        return (
            <>
                <ul>
                    {users.map(user => <li key={user.id}>
                        {user.username} ({user.name})
                    </li>)}
                </ul>
                <button onClick={fetchUsers}>다시 불러오기</button>
            </>);
    }

    export default Users;
    ```

- useReducer를 이용하면 다음과 같음

    ```javascript
    import React, {useEffect, useReducer} from 'react';
    import axios from 'axios';

    function reducer(state, action) {
        switch (action.type) {
            case 'LOADING':
                return {
                    loading: true,
                    data: null,
                    error: null,
                }
            case 'SUCCESS':
                return {
                    loading: false,
                    data: action.data,
                    error: null,
                }
            case 'ERROR':
                return {
                    loading: false,
                    data: null,
                    error: action.error,
                }
            default:
                throw new Error('Unhandled action type: ${action.type}');
        }
    }

    function Users() {
        const [state, dispatcher] = useReducer(reducer, {
            loading: false,
            data: null,
            error: null,
        });
        const fetchUsers = async () => {
            dispatcher({ type: 'LOADING' });
            try {
                const response = await axios.get(
                    'https://jsonplaceholder.typicode.com/users/');
                dispatcher({ type: 'SUCCESS', data: response.data });
            } catch (e) {
                dispatcher({ type: 'ERROR', error: e });
            }
        };

        useEffect(() => {        
            fetchUsers();
        }, []);

        const {loading, data: users, error } = state;

        if (loading) return <div>로딩중...</div>
        if (error) return <div>에러가 발생했습니다.</div>
        if (!users) return null;

        return (
            <>
                <ul>
                    {users.map(user => <li key={user.id}>
                        {user.username} ({user.name})
                    </li>)}
                </ul>
                <button onClick={fetchUsers}>다시 불러오기</button>
            </>);
    }

    export default Users;
    ```

- useAsync 커스텀 Hook 만들어서 사용하기

    ```javascript
    import { useReducer, useEffect, useCallback} from 'react';

    function reducer(state, action) {
        switch (action.type) {
            case 'LOADING':
                return {
                    loading: true,
                    data: null,
                    error: null,
                }
            case 'SUCCESS':
                return {
                    loading: false,
                    data: action.data,
                    error: null,
                }
            case 'ERROR':
                return {
                    loading: false,
                    data: null,
                    error: action.error,
                }
            default:
                throw new Error('Unhandled action type: ${action.type}');
        }
    }

    export default function useAsync(callback, deps = []) {
        const [state, dispatch] = useReducer(reducer, {
            loading: false,
            data: null,
            error: null,
        });

        const fetchData = useCallback(async () => {
            dispatch({type: 'LOADING'});
            try {
                const data = await callback();
                dispatch({type: 'SUCCESS', data});
            } catch (e) {
                dispatch({type: 'ERROR', error: e});
            }
        }, [callback]);

        useEffect(() => {
            fetchData();
        }, deps);

        return [state, fetchData];
    }
    ```

    ```javascript
    import React from 'react';
    import axios from 'axios';
    import useAsync from './useAsync';

    async function getUsers() {
        const response = await axios.get(
            'https://jsonplaceholder.typicode.com/users/');

        return response.data;
    }

    function Users() {
        const [state, refetch] = useAsync(getUsers, []);
        
        const {loading, data: users, error } = state;

        if (loading) return <div>로딩중...</div>
        if (error) return <div>에러가 발생했습니다.</div>
        if (!users) return null;

        return (
            <>
                <ul>
                    {users.map(user => <li key={user.id}>
                        {user.username} ({user.name})
                    </li>)}
                </ul>
                <button onClick={refetch}>다시 불러오기</button>
            </>);
    }

    export default Users;
    ```

- React-Async로 요청 상태 관리하기

    - react-async는 라이브러리임
    ```
    $ yarn add react-async
    ```

    ```javascript
    import React from 'react';
    import axios from 'axios';
    import { useAsync } from 'react-async';

    async function getUsers({ id }) {
        const response = await axios.get(
            'https://jsonplaceholder.typicode.com/users/${id}');

        return response.data;
    }

    function Users({ id }) {
        const {
            data: user,
            error,
            isLoading,
            reload
        } = useAsync({
            promiseFn: getUser,
            id,
            watch: id,
        });
        
        const {loading, data: users, error } = state;

        if (isLoading) return <div>로딩중...</div>
        if (error) return <div>에러가 발생했습니다.</div>
        if (!users) return null;

        return (
            <div>
                <h2>{user.username}</h2>
                <p>
                    <b>Emai: </b> {user.email}
                </p>
            </div>
        );
    }

    export default Users;
    ```
    - 단점: 옵션이 너무나도 많음

- Context에서 비동기 작업 상태 관리하기

    - 특정 요청이 다양한 곳에 이용할때 이용됨

    ```javascript
    import React, { createContext, useReducer, useContext } from 'react';
    import axios from 'axios'

    const initialSate = {
        users: {
            loading: false,
            data: null,
            error: null,
        },
    }

    const loadingState = {
        loading: true,
        data: null,
        error: null,
    };

    const succes = (data) => ({
        loading: false,
        data,
        error: null,
    });

    const error = e => ({
        loading: false,
        data: null,
        error: e,
    })

    function usersReducer(state, action) {
        switch (action.type) {
            case 'GET_USERS':
                return {
                    ...state,
                    users: loadingState
                };
            case 'GET_USERS_SUCCESS':
                return {
                    ...state,
                    users: success(action.data),
                };
            case 'GET_USERS_ERROR':
                return {
                    ...state,
                    users: error(action.error),
                };
            default:
                throw new Error('Unhandled action type', action.type);
        }
    }

    const UsersStateContext = createContext(null);
    const UsersDispatchContext = createContext(null);


    export function UsersProvider({children}) {
        const [state, dispatch] = useReduder(usersReducer, initialState);

        return (
            <UsersStateContext.Provider value={context}>
                <UsersDispatchContext.Provider value={dispatch}>
                    {children}
                </UsersDispatchContext.Provider>
            </UsersStateContext.Provider>
        )
    }

    export function useUsersState() {
        const state = useContext(UsersStateContext);
        if (!state) {
            throw new Error('Cannot find UserProvider');
        }

        return state;
    }

    export function useUsersDispatch() {
        const dispatch = useContext(UsersDispatchContext);
        if (!dispatch) {
            throw new Error('Cannot find UserProvider');
        }

        return dispatch;
    }

    export async function getUsers(dispatch) {
        dispatch({ type: 'GET_USERS'});
        try {
            const response = await axios.get(
                'https://jsonplaceholder.typicode.com/users');
            dispatch({ type: 'GET_USERS_SUCCESS', data: response.data })
        } catch (e) {
            dispatch({
                type: 'GET_USERS_ERROR', error: e
            })
        }
    }
    ```

    ```javascript
    import React from 'react';
    import Users from './Users';
    import { UsersProvider } from './UsersContext'

    function App {
        return (
            <UsersProvider>
                <Users />
            </UsersProvider>
        )
    }

    export default App;
    ```

    ```javascript
    import React, { useState } from 'react';
    
    function Users() {                
        const [userId, setUserId] = useState(null);
        const state = useUsersState();
        const dispatch = useUsersDispatch();

        const {loading, data: users, error} = state.users;

        const fetchData = () => {
            getUsers(dispatch);
        }

        if (loading) return <div>로딩중...</div>
        if (error) return <div>에러가 발생했습니다.</div>
        if (!users) return null;

        return (
            <>
                <ul>
                    {users.map(user => <li key={user.id}>
                        {user.username} ({user.name})
                    </li>)}
                </ul>
                <button onClick={fetchData}>다시 불러오기</button>
            </>);
    }

    export default Users;
    ```