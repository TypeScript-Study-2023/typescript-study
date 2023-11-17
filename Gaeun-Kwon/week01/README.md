# **Summary 🧚‍♀️**

- 학습 범위: ts-all-in-one 강의 (~섹션01. 타입을 집합으로 생각하자(좁은 타입과 넓은 타입))
- [ts-all-in-one 레포지토리](https://github.com/ZeroCho/ts-all-in-one) 리드미에 필요한 자료와 교안이 강의 레포에 한 방에 정리되어 있어서 좋았다. 핸드북을 모아둔 핸드북 같은 느낌.
- 이번주는 TypeScript 기본 문법을 훑어보았다.
몰랐던 내용도 있었고 무의식적으로 잘 지키고 있는 것들도 있었는데, 한 번 정리할 수 있어서 좋았다. 명확히 정리하면 잘 사용하고 잘 읽을 수 있을테니 좋다!

# **Concept 🧐**

> [ts-all-in-one 레포지토리](https://github.com/ZeroCho/ts-all-in-one)의 리드미를 바탕으로
새롭게 알게 된 내용이나, 알고 있었지만 한 번 더 정리하고 싶은 내용을 기록하였습니다.
> 
- tsc 의 역할 (각각의 역할은 별개. ex.에러가 있어도 js 변환은 됨.)
    - TS → JS 변환
    - 코드 자체의 타입을 검사해줌
- 기본 문법
    - 기본적으로 변수, 속성, 매개변수, 리턴값에 타입이 붙었다고 생각하면 된다.
    - 타입 자리가 헷갈리면 지워서 JS 문법이 되는 걸 찾자.
    
    ```tsx
    const a: number = 5;
    
    // 함수 타이핑하는 두 가지 방법
    function add(x: number, y: number): number { return x + y }
    const add: (x: number, y: number) => number = (x, y) => x + y;
    
    // 객체
    const obj: { lat: number, lon: number } = { lat: 37.5, lon: 127.5 };
    
    // 배열 타이핑하는 두 가지 방법
    const arr: string[] = ['123', '456'];
    const arr: Array<number> = ['123', '456'];
    
    const tuple: [number, number, string] = [123, 456, 'hello'];
    
    // 값을 고정할 수 있음.
    const f: true = true;
    const f: 5 = 5;
    ```
    
    - 특수한 타입 {} (null과 undefined가 아닌 모든 타입)
    
    ```tsx
    const z: {} = 5;
    ```
    
- 타입 추론
    - 타입스크립트는 타입추론을 해준다.
    - TS가 타입 추론을 정확하게 해줬다면, 굳이 타이핑해주지 않고 그대로 사용하면 된다.
    (오히려 더 부정확한 타입이 될 수도 있음)
    
    ```tsx
    // TS는 아래 코드에서 a = "5" 라고 타입추론.
    const a = '5'; 
    
    // 타입을 직접 지정해주면서 오히려 string 이라는 더 부정확한 타입으로 바뀜. 👎
    const a: string = '5';
    ```
    
    - 그럼 타입추론을 잘 해줬는지는 어떻게 알지?
    
    ```tsx
    // 아래에서, TS는 return값 & result 타입 지정해주지 않았음에도 타입추론 잘 해줌.
    function add(x: number, y: number) { return x + y }
    const result = add(12, 34);
    
    // -> 매개변수 중 하나의 타이핑을 지우면, result 의 추론된 타입은 any 가 됨.
    // 지워보면서 타입추론 잘 해주고 있는지 확인하는 것도 방법이다.
    ```
    
- 타입에 대한 부분은 Javascript 변환 시 사라진다. 그 사라지는 부분을 잘 파악해두자.
    - : 뒷부분, as 뒷부분, <> 부분, interface, type, function 일부 … 등등 사라짐.
    
    ```tsx
    function add(x: number, y:number): number; // 타입 (-> JS 변환 시 사라짐)
    function add(x, y) { // 실제 코드
    		return x + y;
    }
    
    // 변환된 JS 코드
    function add(x, y) { 
    		return x + y;
    }
    ```
    
    ```tsx
    let aa = 123;
    aa = 'hello' as unknown as number;
    
    // 변환된 JS 코드
    let aa = 123;
    aa = 'hello';
    ```
    
- any 를 최대한 쓰지 않는 것을 목표로 할 것. 쓰더라도 나중에 꼭 제대로 타이핑하기.
- never 타입
    - never, unknown, any 타입 주의하자.
    - [never에 대한 좋은 설명 글](https://ui.toast.com/weekly-pick/ko_20220323) 
    - 빈 배열 초기화 시, 타이핑 해주지 않으면 never 타입이 뜰 수 있으니 타이핑 해주자.
    - 최대한 ! 대신 if를 쓰자.
    
    ```tsx
    // 무조건 null 이나 undefined 가 아님을 보장하는 방법: 마지막에 ! 붙이기
    const head = document.querySelector('#head')!;
    console.log(head);
    
    // 하지만, 안전하게 if를 사용하자.
    const head = document.querySelector('#head');
    if (head) {
      console.log(head);
    }
    ```
    
- string과 String은 다름. 소문자로 하는 것 기억하자.
    
    ```tsx
    const a: string = 'hello';
    const b: String = 'hell';
    ```
    
- 템플릿 리터럴 타입 (이라는 게 존재한다.) (유니언 등 사용 가능)
    
    ```tsx
    type World = "world" | "hell";
    
    // type Greeting = "hello world" | "hello hell"
    type Greeting = `hello ${World}`;
    ```
    
    ```tsx
    type Greeeting = `hello ${string}`;
    
    const a:Greeeting = 'hello something'; // hello 다음 모든 문자열 가능
    ```
    
- rest
    
    ```tsx
    function rest(a:string, ...args: string[]) {
        console.log(a, args); // "1",  ["2", "3"]
    }
    
    rest('1', '2', '3');
    ```
    
- 튜플
    
    ```tsx
    const tuple: [string, number] = ['1', 1];
    
    tuple[2] = 'hello'; // error
    tuple.push('hello'); // TS가 못 막음.바보.
    ```
    
- enum
    - 여러개의 변수들을 하나의 그룹으로 묶고싶을 때 주로 사용.
    - JS 변환 시 사라짐.
    
    ```tsx
    const enum EDirection {
      Up = 1,
      Down,
      Left,
      Right,
    }
    
    const a = EDirection.Up; // 1
    const b = EDirection.Left; // 3
    ```
    
    - 아래와 같이 객체를 사용하면, JS 변환 시 as const 를 제외하고 남아있음.
        - as const: 이 값을 그대로 타입으로, 상수화해서 사용하겠다는 선언.
        - 앞에 readonly를 작성하는 것도 방법.
    
    ```tsx
    const ODirection = {
      Up: 0,
      Down: 1,
      Left: 2,
      Right: 3,
    } as const;
    ```
    
    ```tsx
    // Using the enum as a parameter
    // dir: 이것 중 하나(4개 중 하나)
    function walk(dir: EDirection) {}
     
    // It requires an extra line to pull out the keys
    type Direction = typeof ODirection[keyof typeof ODirection];
    function run(dir: Direction) {}
     
    walk(EDirection.Left);
    run(ODirection.Right);
    ```
    
- typeof
    - JS 값을 타입으로 사용하고 싶을 때, 앞에 typeof 를 붙여주자.
    
    ```tsx
    const obj = { a: '123', b: 'hello', c: 'world' } as const;
    
    type Key = keyof typeof obj; // key만 뽑아 타입으로 사용.
    
    type Value = typeof obj[keyof typeof obj]; // value만 뽑아 타입으로 사용.
    
    const a: Key = 'a'; // "a" | "b" | "c"
    const b: Value = 'hello'; // "123" | "hello" | "world"
    ```
    
- union(|), intersection(&)
    - union 타입을 적절하게 사용하지 않으면, 타입추론이 잘 안 된다.
    
    ```tsx
    // 잘못된 예시
    function add(x: string | number, y: string | number): string | number { return x + y }
    
    const result : string | number = add(1, 2)
    ```
    
    - intersection
        - 객체를 대상으로 주로 사용하게 된다.
    
    ```tsx
    type A = {
        a: string;
    }
    type B = {
        b: string;
    }
    
    // 모든 속성을 만족하면 된다.
    const aa: A & B = { a: 'hello', b: 'world' };
    
    // 여러 개 중 하나만 만족하면 된다.
    let bb: A | B = { a: 'hello', b: 'world' };
    bb: A | B = { a: 'hello' };
    bb: A | B = { b: 'world' };
    ```
    
- type, interface 확장
    
    ```tsx
    type Animal = { breathe: true };
    type Mammal = Animal & { breed: true };
    type Human = Mammal & { think: true };
    ```
    
    ```tsx
    interface Animal {
        breathe: true;
    }
    interface Mammal extends Animal {
        breed: true;
    }
    ```
    
- 네이밍 룰
    - prefix로 I-, T-, E- 붙이는 경우도 있다. 사실 호버 시 나타남. 컨벤션 따라 가면 될 듯.
- 타입을 집합으로 생각하자.
    
    ```tsx
    type A = { name: string };
    type B = { age: number };
    
    type AB = A | B;
    
    type C = A & B;
    
    const ab: AB = { name: 'gaeun' };
    const c: C = { name: 'gaeun', age: 25 };
    ```
    
    - 그런데, 객체 리터럴은 잉여 속성 검사가 있음.
        
        ```tsx
        const c: C = { name: 'gaeun', age: 25, married: false }; // error.
        
        // 객체 리터럴을 바로 넣지 않고, 변수로 한 번 빼면 됨.
        const obj = { name: 'gaeun', age: 25, married: false };
        const c: C = obj;
        ```
        

# Questions 🤔

> 추후 살펴보고 싶은 자료나, 질문이 있는 부분을 적어두었습니다.
> 
- [never에 대한 좋은 설명 글](https://ui.toast.com/weekly-pick/ko_20220323)
    - [switch, if-else 문의 모든 상황을 보장한다.](https://ui.toast.com/weekly-pick/ko_20220323#switch-if-else-%EB%AC%B8%EC%9D%98-%EB%AA%A8%EB%93%A0-%EC%83%81%ED%99%A9%EC%9D%84-%EB%B3%B4%EC%9E%A5%ED%95%9C%EB%8B%A4)