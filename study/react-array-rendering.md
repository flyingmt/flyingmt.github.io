### 배열 렌더링하기

- 여려개의 똑같은 항목들을 표출하려면, 그것을 표출하는 컴포넌트를 만들어서 사용함.

    ```javascript
    import React from 'react';        

    function User({ user }) {
        return (
            <div>
                <b>{user.username</b> <span>({user.email})</span>
            </div>
        );
    }

    function UserList() {
        const users = []; // 배열 정의
        return (
            <User user={users[0]} />
            <User user={users[1]} />
            <User user={users[2]} />
        );
    }

    export default UserList;
    ```
- 그리고 함수 map을 활용함. 여기서 각 항목은 key를 제공해야 함.

    ```javascript
    import React from 'react';        

    function User({ user }) {
        return (
            <div>
                <b>{user.username}</b> <span>({user.email})</span>
            </div>
        );
    }

    function UserList() {
        const users = []; // 배열 정의
        return (
            {
                users.map(
                    user => (<User user={user} key={user.id} />)
                )
            }
        );
    }

    export default UserList;
    ```
- 조금 더 코드 정리하기 (useRef로 컴포넌트 안의 변수 만들기 / 렌더링이 필요없을 때)
    ```javascript
    import React, {useRef} from 'react';        

    function App() {
        const users = []; // 배열 정의

        const nextId = useRef(4);

        const onCreate = () => {
            console.log(nextId.current); // 4
            nextId.current += 1;
        };

        return (
            <UserList users={users} />
        );
    }

    export default App;
    ```
    ```javascript
    import React from 'react';        

    function User({ user }) {
        return (
            <div>
                <b>{user.username}</b> <span>({user.email})</span>
            </div>
        );
    }

    function UserList({ users }) {
        
        return (
            {
                users.map(
                    user => (<User user={user} key={user.id} />)
                )
            }
        );
    }

    export default UserList;
    ```
- 배열에 항목 추가하기 (spread을 이용하는 방법과 concat을 이용하는 방법)
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

    export default CreateUser;
    ```
    ```javascript
    import React, {useRef, useState} from 'react';        

    function App() {
        const [users, setUsers] = userState([]); // 배열 정의
        const [inputs, setInputs] = userState({
            username: '',
            email: '',
        });
        const {username, email} = inputs;

        const onChange = e => {
            const {name, value} = e.target;
            setInputs({
                ...inputs,
                [name]: value
            });
        };

        const nextId = useRef(4);

        const onCreate = () => {
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
        };

        return (
            <>
                <CreateUser 
                    username={username}
                    email={email}
                    onChange={onChange}
                    onCreate={onCreate} />
                <UserList users={users} />
            </>
        );
    }

    export default App;
    ```    

- 배열에서 제거하기 (filter 사용하기 : 불변성, onClick에서 호출하는 함수를 만들어야 함)

    ```javascript
    import React from 'react';        

    function User({ user, onRemove }) {
        const {username, email, id } = user;
        return (
            <div>
                <b>{username}</b> <span>({email})</span>
                <button onClick={() => onRemove(id)}>삭제</button>
            </div>
        );
    }

    function UserList({users, onRemove}) {
        return (
            {
                users.map(
                    (user, index) => (
                        <User 
                            user={user} 
                            key={user.id} 
                            onRemove={onRemove} />
                    )
                )
            }
        );
    }

    export default UserList;
    ```
    ```javascript
    const onRemove = id => {
        setUsers(users.filter(user => user id !== id));
    }    

    return (
            <UserList users={users} onRemove={onRemove}/>
        );
    ```
- 배열 수정하기 (Users에 active 항목 추가해서)

    ```javascript
    import React from 'react';        

    function User({ user, onRemove, onToggle }) {
        const {username, email, id, active } = user;
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

    ```javascript
    const onToggle = id => {
        setUsers(users.map(
            user => user.id === id
                ? {...user, active: !user.active}
                : user
        ));
    }    

    return (
            <UserList 
                users={users} 
                onRemove={onRemove}
                onToggle={onToggle}/>
        );
    ```

