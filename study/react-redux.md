## React Redux

### 패키지 추가하기
    ```
    $ yarn add redux
    ```

### Redux 모듈 만들기

- Redux 모듈은 다음을 포함되어야 함
    - 액션 타입 정의
    - 액션 생성 함수 정의
    - 초기 상태 정의
    - 리듀서 정의

- Redux 모듈을 만드는 방법
    - 보통 액션과 리듀서 정의를 다른 파일로 분류해서 만드는 방법이 있음
    - 요즘에는 Ducks 패턴으로 모두 한파일에 추가하는 방법이 있음
        - 기능별 리듀서를 각자 파일로 만듬
        - 기능별 리듀서를 합친 Root 리듀서를 만듬 (combineReducers를 사용)

- Ducks 패턴으로 만들기
    - 모듈 만들기
        - modules > counter.js
            ```javascript
            // 액션 타입 정의
            const SET_DIFF = "counter/SET_DIFF";
            const INCREASE = "counter/INCREASE";
            const DECREASE = "counter/DESCREASE";

            // 액션 정의
            export const setDeff = (diff) => ({ type: SET_DIFF, diff });
            export const increase = () => ({ type: INCREASE });
            export const decrease = () => ({ type: DECREASE });

            // 초기 상태 정의
            const initialState = {
            number: 0,
            diff: 1,
            };

            // 리듀서 정의
            export default function counter(state = initialState, action) {
            switch (action.type) {
                case SET_DIFF:
                return {
                    ...state,
                    diff: action.diff,
                };
                case INCREASE:
                return {
                    ...state,
                    number: state.number + state.diff,
                };
                case DECREASE:
                return {
                    ...state,
                    number: state.number - state.diff,
                };
                default:
                return state;
            }
            }

            ```
        - modules > todos.js
            ```javascript
            // 액션 타입 정의
            const ADD_TODO = "todos/ADD_TODO";
            const TOGGLE_TODO = "todos/TOGGLE_TODO";

            // 액션 정의
            let nextId = 1;
            export const addTodo = (text) => ({
            type: ADD_TODO,
            todo: {
                id: nextId++,
                text,
            },
            });
            export const toggleTodo = (id) => ({
            type: TOGGLE_TODO,
            id,
            });

            // 초기 상태 정의
            const initialSate = [
            /*
                {
                    id: 1,
                    text: '예시'
                    done: false
                }
                */
            ];

            // 리듀서 정의
            export default function todos(state = initialSate, action) {
            switch (action.type) {
                case ADD_TODO:
                return state.concat(action.todo);
                case TOGGLE_TODO:
                return state.map((todo) =>
                    todo.id === action.id ? { ...todo, done: !todo.done } : todo
                );
                default:
                return state;
            }
            }

            ```
        - modules > index.js
            ```javascript
            import { combineReducers } from 'redux';
            import counter from './counter';
            import todos from './todos';

            const rootReducer = combineReducers({
                counter,
                todos,
            });

            export default rootReducer;
            ```

        - 컴포넌트에 사용하기 위해서는 다음 패키지 추가
            ```
            $ yarn add react-redux
            ```

        - index.js
            ```javascript
            import React from 'react';
            import ReactDOM from 'react-dom';
            import './index.css';
            import App from './App';
            import * as serviceWorker from './serviceWorker';
            import { Provider } from 'react-redux';
            import { createStore } from 'redux';
            import rootReducer from './modules';

            const store = createStore(rootReducer);

            ReactDOM.render(
            <Provider store={store}>
                <App />
            </Provider>,
            document.getElementById('root')
            );

            // If you want your app to work offline and load faster, you can change
            // unregister() to register() below. Note this comes with some pitfalls.
            // Learn more about service workers: https://bit.ly/CRA-PWA
            serviceWorker.unregister();

            ```
    - 컴포넌트 구현하기 (props만 사용)

        - components > Counter.js
        
        ```javascript
        import React from "react";

        function Counter({ number, diff, onIncrease, onDecrease, onSetDiff }) {
        const onChange = (e) => {
            onSetDiff(parseInt(e.target.value, 10));
        };
        return (
            <div>
            <h1>{number}</h1>
            <div>
                <input type="number" value={diff} onChange={onChange} />
                <button onClick={onIncrease}>+</button>
                <button onClick={onDecrease}>-</button>
            </div>
            </div>
        );
        }

        export default Counter;

        ```

    - 컴포넌트 구현하기 (컨테이너 구현하기)

        - containers > CounterContainer.js

        ```javascript
        import React from "react";
        import Counter from "../components/Counter";
        import { useSelector, useDispatch } from "react-redux";
        import { setDiff, increase, decrease } from "../modules/counter";

        function CounterContainer() {
        const { number, diff } = useSelector((state) => ({
            number: state.counter.number,
            diff: state.counter.diff,
        }));
        const dispatch = useDispatch();
        
        const onSetDiff = (diff) => dispatch(setDiff(diff));
        const onIncrease = () => dispatch(increase());
        const onDecrease = () => dispatch(decrease());

        return (
            <Counter
            number={number}
            diff={diff}
            onIncrease={onIncrease}
            onDecrease={onDecrease}
            onSetDiff={onSetDiff}
            />
        );
        }

        export default CounterContainer;
        ```

