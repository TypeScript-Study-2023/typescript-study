# [TS][3주차] - 2023.12.09(토)

# **Summary 🧚‍♀️**

- 학습 범위: ts-all-in-one 강의 (섹션02)
- lib.es5.d.ts를 분석하면서, 타입스크립트가 어떤 식으로 타입을 추론해가는지 반복적으로 살펴보았습니다. 연습 👍👍

# **Concept 🧐**

### [**section 02] lib.es5.d.ts 분석**

**제네릭 분석**

- forEach 제네릭 분석
    
    ```tsx
    // 선언 시 T의 타입이 정해지지 않지만, 사용 시 정해져서 잘 추론되는 것이 보임 :+1
    interface Array<T> {
    	forEach(callbackfn: (value: T, index: number, array: T[]) => void, thisArg?: any): void;
    }
    
    [1,2,3].forEach((value) => {console.log(value)}) // value:number 추론
    ```
    
    - ex) 하나라도 타입 있으면 TS 가 타입 추론함 🧠
        
        ```bash
        function add<T>(a: T,b: T): T { return a }
        
        const a = add(1, 2)
        
        add<number>(1, 2)
        add(1, 2)
        add('1', '2')
        add(1, '2') // error
        ```
        
- every 제네릭 분석
    
    ```tsx
    // S 는 T 라는 타입의 부분집합
    // 커스텀 타입가드 사용
    interface Array<T> {
    	every<S extends T>(predicate: (value: T, index: number, array: T[]) => value is S, thisArg?: any): this is S[];
    }
    ```
    
- map 제네릭 분석
    
    > TS oO (
    	배열에 들어온 값 타입이 number이므로 T 추론. 
    	콜백함수의 리턴값의 타입이 string이므로 U 추론. 
    	=> map 함수의 리턴값 string[] 추론.
    	=> strings의 타입은 string[] 추론. 야호!
    )
    > 
    
    ```tsx
    interface Array<T> {
    	map<U>(callbackfn: (value: T, index: number, array: T[]) => U, thisArg?: any): U[];
    }
    
    const strings = [1, 2, 3].map((item) => item.toString());
    ```
    
- filter 제네릭 분석
    
    > TS oO (
    	배열에 들어온 값 타입이 number이므로 T 는 number 추론.
    	'value is number' 이므로 S 는 number 추론.
    	=> filtered의 타입은 number[] 추론. 야호.
    )
    > 
    
    ```tsx
    interface Array<T> {
    	filter<S extends T>(predicate: (value: T, index: number, array: T[]) => value is S, thisArg?: any): S[];
    }
    
    const filtered = [1, 2, 3, 4, 5].filter((value) => value % 2);
    ```
    
    문제발생) 아래의 경우, 타입 추론 제대로 못 해줌.
    TS 가 추론해준 filtered 의 타입은 `string[]` 이 아닌, `(string | number)[]` . 👎
    
    > 이유: T가 (string | number) 로 추론됨
    	=> value is S 
    	=> S는 (string | number) 로 추론됨 
    	=> 리턴값 (string | number)[] 로 추론.
    > 
    
    ```tsx
    const filtered = [1, '2', 3, '4', 5].filter((value) => typeof value === 'string');
    ```
    
    그럼 어떻게 고쳐줄까?
    
    > S를 우리가 원하는 걸로 지정해서 TS 에게 직접 알려주자.
    이게 가능한 이유는? `string extends string | number` 이므로.
    > 
    
    ```tsx
    const predicate = (value: string | number): value is string => typeof value === 'string';
    
    const filtered = [1, '2', 3, '4', 5].filter(predicate);
    ```
    

**타입 직접 만들어보기**

> 함수 사용부 작성해놓고 → 해당 함수의 타이핑 해보는 연습을 해보자!
> 
- forEach 타입 직접 만들기
    
    ```tsx
    interface Arr<T> {
    	/* 실제 작성해본 타입 */
      forEach(callback: (item: T) => void): void; 
    	/* 실제 타입 */
    	forEach(callbackfn: (value: T, index: number, array: T[]) => void, thisArg?: any): void;
    }
    
    const a: Arr<number> = [1, 2, 3];
    a.forEach((item) => {
      console.log(item);
      item.toFixed(1);
    });
    a.forEach((item) => {
      console.log(item);
      return '3';
    });
    
    const b: Arr<string> = ['1', '2', '3'];
    b.forEach((item) => {
      console.log(item);
      item.charAt(3);
    });
    ```
    
- map 타입 직접 만들기
    
    ```tsx
    interface Arr<T> {
      forEach(callback: (item: T) => void): void;
      map<S>(callback: (v: T, i: number) => S): S[];
    }
    
    const a: Arr<number> = [1, 2, 3];
    
    const A = a.map((v, i) => v + 1); // [2, 3, 4] number[] 추론.
    const B = a.map((v, i) => v.toString()); // ['2', '3', '4'] string[] 추론.
    const C = a.map((v, i) => v % 2 === 0); // boolean[] 추론.
    
    const b: Arr<string> = ['1', '2', '3'];
    
    const D = b.map((v) => +v); // number[] 추론.
    ```
    
- filter 타입 직접 만들기
    
    ```tsx
    interface Arr<T> {
      filter(callback: (v: T) => boolean): T[]; // X
    
      filter<S extends T>(callback: (v: T) => v is S): S[]; // O
    }
    
    const a: Arr<number> = [1, 2, 3];
    const A = a.filter((v) => v % 2 === 0);
    
    const b: Arr<number | string> = [1, '2', 3, '4', 5];
    const B = b.filter((v) => typeof v === 'string'); // string[] 이 추론되어야 함
    ```
    

- 공변성과 반공변성
    
    ```tsx
    function a(x: string): number {
      return 0;
    }
    type B = (x: string) => number | string;
    let b: B = a;
    
    function a(x: string): number | string {
      return 0;
    }
    type B = (x: string) => number;
    let b: B = a;
    
    function a(x: string | number): number {
      return 0;
    }
    type B = (x: string) => number;
    let b: B = a;
    
    function a(x: string): number {
      return 0;
    }
    type B = (x: string | number) => number;
    let b: B = a;
    ```
    
- 오버로딩
- 타입스크립트는 건망증이 심하다(+에러 처리법)

# **Wrap-up 🍪**

- 다음 스터디까지 lib.es5.d.ts 의 배열 관련 메서드들을 타이핑해보는 숙제 같이 해보면 어떨까요!?