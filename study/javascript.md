## Javascript

### 비동기 처리

- 특정 코드를 비동기로 실행하려면 setTimeout 함수를 사용함
- setTimeout에 2번째 파라미터의 시간은 0ms 으로 설정해도 브라우저에서는 최소 4ms으로 다시설정됨.
    ```javascript
    function work() {
        setTimeout(() => { 
            const start = Date.now();
            for (let i = 0; i < 100000000; i++) {

            }
            const end = Date.now();
            console.log(end - start + 'ms');
        }, 0);
    }
    ```

### Promise

### Async, Await

### Promise All, Promise Race