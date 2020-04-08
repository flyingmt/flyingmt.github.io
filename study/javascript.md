## Javascript

### 비동기 처리

- 특정 코드를 비동기로 실행하려면 setTimeout 함수를 사용함
- 함수 setTimeout에 2번째 파라미터의 시간은 0ms 으로 설정해도 브라우저에서는 최소 4ms으로 다시설정됨.
    ```javascript
    function work() {
        setTimeout(() => { 
            const start = Date.now();
            for (let i = 0; i < 100000000; i++) { }
            const end = Date.now();
            console.log(end - start + 'ms');
        }, 0);
    }

    console.log('작업시작!');
    work();
    console.log('다음작업!');
    ```
- 비동기 함수 실행 후에 무엇인가 실행하고 싶으면 callback을 사용하면 됨. (파라미터로 함수를 넘김)
    ```javascript
    function work(callback) {
        setTimeout(() => { 
            const start = Date.now();
            for (let i = 0; i < 100000000; i++) { }
            const end = Date.now();
            console.log(end - start + 'ms');
            callback(end - start);
        }, 0);
    }

    console.log('작업시작!');
    work((ms) => {
        console.log('작업이 끝남 : ' + ms + 'ms');
    });
    console.log('다음작업!');
    ```
- 주로 다음 작업을 할때 비동기를 사용을 추천함
    - API 요청 (서버에 데이터 요청)
    - 파일 읽기 (서버에 데이터 요청)
    - 암호화/복호화
    - 작업 예약

### Promise

- 비동기 작업을 더 쉽게 하기 위한 ES6에 도입된 기능 (간결한 코드 - 콜백지옥에서 벗어나자)
    ```javascript
    const myPromise = new Promise((resolve, reject) => {
        // 작업 구현
    });
    ```
    작업 성공시 resolve 함수 호출, 작업 실패시 reject 함수 호출 함.

- 다음과 같이 정의한 작업 성공시 다음 작업을 설정하려면 then을 사용함.
    ```javascript
    const myPromise = new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('result');
        }, 1000);
    });

    myPromise.then(result => {
        console.log(result);
    });
    ```
- 다음과 같이 정의한 작업 실패시 다음 작업을 설정하려면 catch을 사용함.
    ```javascript
    const myPromise = new Promise((resolve, reject) => {
        setTimeout(() => {
            reject(new Error());
        }, 1000);
    });

    myPromise.then(result => {
        console.log(result);
    }).catch(e => {
        console.error(e);
    });
    ```
- 함수에서 Promise 사용 방법
    ```javascript
    function increaseAndPrint(n) {
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                const value = n + 1;
                if (value === 5) {
                    const error = new Error();
                    error.name = 'valueIsFiveError';
                    reject(error);
                    return;
                }
                console.log(value);
                resolve(value);
            }, 1000);
        });
    }

    increaseAndPrint(0).then(n => {
        console.log('result: ' + n);
    })
    ```
    위 코드를 다음과 같이 재귀적으로 사용할 수 있음.
    ```javascript
    increaseAndPrint(0).then(n => {
        return increaseAndPrint(n);
    }).then(n => {
        return increaseAndPrint(n);
    }).then(n => {
        return increaseAndPrint(n);
    }).then(n => {
        return increaseAndPrint(n);
    }).then(n => {
        return increaseAndPrint(n);
    }).catch(e => {
        console.error(e);
    });    
    ```
    다시 위 코드를 다음과 같이 정리 할 수 있음.
    ```javascript
    increaseAndPrint(0)
    .then(increateAndPrint)
    .then(increateAndPrint)
    .then(increateAndPrint)
    .then(increateAndPrint)
    .then(increateAndPrint)
    .catch(e => {
        console.error(e);
    });
    ```
- Promise를 사용하면 코드가 더 깊어지지 않은 문제는 해결되나
    - 여러개의 비동기 작업이 있을때 어디서 에러가 났는지 구분하기 어려우며
    - 분기 작업 코드를 추가하기 어렵고,
    - 특정 값을 공유하는 기능도 구현하기 어려움

### Async, Await

- Aync와 Await를 사용하면 Promise를 더 쉽게 사용할 수 있음
    ```javascript
    function sleep(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }

    async function process() {
        console.log('안녕하세요~');
        await sleep(1000);
        console.log('반갑습니다~');
    }

    process();
    ```    
    Promise 함수를 사용하는 함수 앞에 async를 추가하고, Promise 함수를 호출 하는 곳에 await를 추가함.

- 분기점을 만들기 쉽고, 특정 값을 공유하는 기능도 구현하기 쉬움.
- 함수 앞에 async가 있으면 Promise를 반환함.
    ```javascript
    function sleep(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }

    async function process() {
        console.log('안녕하세요~');
        await sleep(1000);
        console.log('반갑습니다~');
        return true;
    }

    process().then(value => {
        console.log(value);
    });
    ```
- 예외를 발생하고 싶으면 throw를 사용하고, 예외를 잡고 싶으면 try/catch를 사용함.
     ```javascript
    function sleep(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }

    async function makeError() {
        await sleep(1000);
        const error = new Error();
        throw error;
    }

    async function process() {
        try {
            await makeError();
        } catch(e) {
            console.error(e);
        }
    }

    process();
    ```


### Promise All, Promise Race

- 여러개 비동기 함수 (화살표를 이용한 정의)
    ```javascript
    function sleep(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }

    const getDog = async () => {
        await sleep(1000);
        return '멍멍이';
    }

    const getRabbit = async () => {
        await sleep(500);
        return '토끼';
    }

    const getTurtle = async () => {
        await sleep(3000);
        return '거북이';
    }

    async function process() {
        const dog = await getDog();
        console.log(dog);
        const rabbit = await getRabbit();
        console.log(rabbit);
        const turtle = await getTurtle();
        console.log(turtle);
    }

    process();
    ```
    위는 각각 비동기 함수들이 동기로 실행됨. 비동기로 실행하려면 다음과 같이 해야함.
    ```javascript
    async function process() {
        const results = await Promise.all(
            [getDog(), getRabbit(), getTurtle()]
        );
        console.log(results);
    }
    ```
    위 코드는 각각 동시에 실행되며, 결과 값이 배열로 반환됨.

- Promise.all을 사용해서 각각 함수에 대한 결과 값을 받고 싶으면
    ```javascript
    async function process() {
        const results = await Promise.all(
            [getDog(), getRabbit(), getTurtle()]
        );
        const dog = results[0];
        const rabbit = results[1];
        const turtle = results[2];
    }
    ```
    배열 비구조 할당, 구조 분해 문법을 사용하여 단순화할 수 있음
    ```javascript
    async function process() {
        const [dog, rabbit, turtle] = await Promise.all(
            [getDog(), getRabbit(), getTurtle()]
        );
    }
    ```
- 여러개의 비동기 함수 중에 가장 빠른 것을 받으려면 Promise.race를 사용함
    ```javascript
    async function process() {
        const first = await Promise.race(
            [getDog(), getRabbit(), getTurtle()]
        );
    }
    ```