## React Immer

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
