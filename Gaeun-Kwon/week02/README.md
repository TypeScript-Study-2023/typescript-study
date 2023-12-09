# **Summary 🧚‍♀️**

-   학습 범위: ts-all-in-one 강의 (~섹션01 끝.)
-   1주차에 이어 타입스크립트 기본 문법을 정리, 학습했습니다.

# **Concept 🧐**

> [ts-all-in-one 레포지토리](https://github.com/ZeroCho/ts-all-in-one)의 리드미를 바탕으로
> 새롭게 알게 된 내용이나, 알고 있었지만 한 번 더 정리하고 싶은 내용을 기록하였습니다.

### section 01.

-   `void` 타입
    -   함수에 직접적인 타이핑이 void로 되어있는 경우, return값이 없다는 뜻.
    ```tsx
    function a(): void {
        return;
        return undefined;
    }

    const b = a();
    ```
    -   메서드나 매개변수에서는 return값 사용 가능하다.
        -   이때는 return값을 사용하지 않겠다는 뜻.
    ```tsx
    interface A {
        talk: () => void;
    }
    const a: A = {
        talk() {
            return 3;
        }, // -> ok
    };

    function a(callback: () => void): void {}
    a(() => {
        return '3'; // -> ok
    });
    ```
    -   지만! 조심해서 사용하자.
    ```tsx
    interface A {
        talk: () => void;
    }
    const a: A = {
        talk() {
            return 3;
        }, // -> ok
    };

    const b = a.talk(); // b: void 타입. 잘못 만든 타입 🥲
    const b = a.talk() as unknown as number; // 강제로 변환해줄 수는 있음.
    ```
    -   void ≠ undefined
    ```tsx
    declare function forEach<T>(arr: T[], callback: (el: T) => undefined): void; // error.

    declare function forEach<T>(arr: T[], callback: (el: T) => void): void; // ok. 매개변수에서 void 타입 리턴값 사용 가능.

    let target: number[] = [];
    forEach([1, 2, 3], (el) => target.push(el)); // error. push는 number를 return
    ```
-   타입만 선언하고 싶을 때 `declare` (구현은 다른 파일에 있어야 함)

    ```tsx
    declare const a: string;
    declare function a(x: number): number;
    declare class A {}

    // 추후 declare module, declare global, declare namespace도 배움
    ```

-   `unknown` 과 `any`
    -   any를 사용하기 보다는, unknown을 사용하자.
        -   any → 타입 검사 포기. 추후 사용 시 에러 표기 없음. ☹️
        -   unknown → 지금 당장은 타입 모르겠지만, 추후 사용 시 타입 지정해서 사용해야 함.
    -   예시) 에러 처리 (→섹션02 마지막강의 참고)
    ```tsx
    try {
    } catch (e) {
        e.message; // error: 'e' is of type 'unknown'.

        (e as Error).message; // ok
    }
    ```
-   타입 간 대응 가능 표

    -   외울 필요 x. TS가 메시지로 알려준다

    ![CEA7B3F5-5BDD-497A-8222-191382A23228_1_201_a](https://github.com/TypeScript-Study-2023/typescript-study/assets/65700066/3689fb71-d380-41eb-a806-7be1e5adc471)

-   `타입 가드` (타입 좁히기)

    -   as 사용 없이, if-else로 좁혀서 사용하자.
    -   원시값, 배열

    ```tsx
    function numOrStr(a: number | string) {
        if (typeof a === 'string') {
            // 원시값
            a.split(',');
        } else {
            a.toFixed(1);
        }
    }

    function numOrNumArr(a: number | number[]) {
        if (Array.isArray(a)) {
            // 배열
            a.slice(1);
        } else {
            a.toFixed(1);
        }
    }
    ```

    -   클래스 자체의 타입은 typeof 클래스 (typeof A) 이다.

    ```tsx
    class A {
        aaa() {}
    }
    class B {
        bbb() {}
    }
    function aOrB(param: A | B) {
        if (param instanceof A) {
            // 타입 좁히기
            param.aaa();
        }
    }

    aOrB(new A());
    aOrB(new B());
    ```

    -   객체는 속성값 또는 속성으로 타입 추론을 해준다.
        -   꿀팁) 객체 만들때 객체 안에 type: 객체명 속성 하나씩 넣어두기

    ```tsx
    type B = { type: 'b', bbb: string };
    type C = { type: 'c', ccc: string };
    type D = { type: 'c', ddd: string };

    type A = B | C | D;

    // 속성값으로 추론
    function typeCheck(a: A) {
      if (a.type === 'b') {
        a.bbb;    // -> a: B 로 추론.
      } else if (a.type === 'c') {
        a.ccc;    // -> a: C | D 로 추론.
      } else {
        a.ddd;    // -> a: never 로 추론.
      }
    }

    // 속성으로 추론
    function typeCheck(a: A) {
      if ('bbb' in a) {
        a.bbb;    // -> a: B 로 추론.
      } else if ('ccc' in a) {
        a.ccc;    // -> a: C 로 추론.
      } else {
    		...
      }
    }
    ```

-   커스텀 타입 가드

    -   타입을 구분해주는 커스텀 함수를 직접 만들 수 있다. (`is` 사용)

    ```tsx
    interface Cat {
        meow: number;
    }
    interface Dog {
        bow: number;
    }

    // 커스텀 타입 가드
    function catOrDog(a: Cat | Dog): a is Dog {
        if ((a as Cat).meow) {
            return false;
        }
        return true;
    }

    // 사용
    if (catOrDog(cat)) {
        console.log(cat.meow);
    }
    if ('meow' in cat) {
        console.log(cat.meow);
    }
    ```

    -   커스텀 타입 가드 예제

    ```tsx
    const isRejected = (input: PromiseSettledResult<unknown>): input is PromiseRejectedResult => {
        return input.status === 'rejected';
    };
    const isFulfilled = <T,>(input: PromiseSettledResult<T>): input is PromiseFulfilledResult<T> => {
        return input.status === 'fulfilled';
    };

    const promises = await Promise.allSettled([Promise.resolve('a'), Promise.resolve('b')]);

    const errors = promises.filter(isRejected);
    ```

-   {} 와 Object
    -   {}, Object 는 객체 타입 x. 모든 타입 o.(null 과 undefined 제외)
    -   객체 타입은 object
    -   지만! 실제 객체 타이핑에는 object 지양, interface, type, class 사용하자.
-   unknown = `{} | null | undefined` (ts v4.8.0 이후)

-   `readonly`, `index signature`, `mapped type`

    -   readonly

    ```tsx
    interface A {
        readonly a: string;
        b: string;
    }
    ```

    -   index signature: 어떤 값이 오든 다 특정 타입이길 원할 때 사용.

    ```tsx
    type A = { [key: string]: number };

    const aaa: A = { a: 3, b: 4, c: 123 };
    ```

    -   mapped type

    ```tsx
    type B = 'Human' | 'Mammal' | 'Animal';
    type A = { [key in B]: number };

    const aaa: A = { Human: 3, Animal: 4, Mammal: 123 };
    ```

-   `optional`

    -   interface 나 type alias 에서도 가능

    ```tsx
    function abc(a: number, b?: number, c: number?) {}
    abc(1);
    abc(1, 2);
    abc(1, 2, 3);

    // interface 나 type alias 에서도 가능
    let obj: { a: string; b?: string } = { a: 'hello', b: 'world' };
    obj = { a: 'hello' };
    ```

-   `generic`
    -   타입을 변수처럼 만드는 것.
    -   만들어둘 때는 타입을 정확히 모르지만, 실제로 사용할 때 타입이 정해질 수 있도록 해준다.
    -   타입에 대한 함수라고 생각하면 됨. 추론을 활용하자.
    ```tsx
    function add<T>(x: T, y: T): T {
        return x + y; // 여기는 error. 매개변수 부분만 에러 막아줌.
    }

    add<number>(1, 2);
    add(1, 2);
    add<string>('1', '2');
    add('1', '2');

    add(1, '2'); // error.
    ```
    -   제네릭 선언 위치
    ```tsx
    function a<T>() {}
    class B<T>() {}
    interface C<T> {}
    type D<T> = {};
    const e = <T>() => {};
    ```
    -   제네릭 기본값, extends
    ```tsx
    function add<T extends string>(x: T, y: T): T {
        return x + y;
    }

    add(1, 2);
    add('1', '2');

    // <T extends {...}> // 특정 객체
    // <T extends any[]> // 모든 배열
    // <T extends (...args: any) => any> // 모든 함수
    // <T extends abstract new (...args: any) => any> // 생성자 타입
    // <T extends keyof any> // string | number | symbol
    ```
    ```tsx
    // jsx
    const add = <T = unknown,>(x: T, y: T) => ({ x, y });

    const add = <T extends unknown>(x: T, y: T) => ({ x, y });

    const add = <T,>(x: T, y: T) => ({ x, y });
    ```
