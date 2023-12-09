# **Summary 🧚‍♀️**

학습 범위: ts-all-in-one 강의 (void의 두가지 사용법 ~ 섹션01. 기본값 타이핑)

# **Concept 🧐**

> 발표 주제의 이론적 개념 및 필요한 배경 지식을 설명합니다.

### forEach, map 제네릭 분석

`.node_modules/typescript/lib/lib.es5.d.ts`파일을 분석하며 공부하면 좋음!

forEach의 선언

```ts
forEach(callbackfn: (value: T, index: number, array: readonly T[]) => void, thisArg?: any): void;
```

[1,2,3].forEach((value) => {console.log(value)}); type number  
['1','2','3'].forEach((value) => {console.log(value)}); type string  
[true,false,true].forEach((value) => {console.log(value)}); type boolean  
[1,'2',true].forEach((value) => {console.log(value)}); type number or string or boolean

callbackfn : (value) => console.log(value)  
T가 한번 정해지는 순간! 나머지 T들도 같이 정해짐  
그래서 1 -> number -> T === number  
'1' -> string -> T === string

map의 선언

```ts
map<U>(callbackfn: (value: T, index: number, array: readonly T[]) => U, thisArg?: any): U[];
```

const strings = [1,2,3].map((item) => item.toString());
return이 item.toString()으로 string type -> U[] : string -> U : string

### filter 제네릭 분석

```ts
filter<S extends T>(predicate: (value: T, index: number, array: readonly T[]) => value is S, thisArg?: any): S[];
filter(predicate: (value: T, index: number, array: readonly T[]) => unknown, thisArg?: any): T[];
```

const filtered = [1,2,3,4,5].filter((value) => value % 2);
에서 return값이 value % 2 !== unknown이므로 위의 filter 정의를 쓰는것이 아닌가...?!

-> 제네릭은 T, K, S를 빈칸 찾기 하듯이 하나씩 채워나가면 됨

> `const filtered = ['1',2,'3',4,'5'].filter((value) => typeof value === 'string');`  
> 의 경우 `typeof filtered === (string \ number)[]`와 같이 나오는데,,, 이유는?!

```tsx
const predicate = (value: string | number): value is string =>
  typeof value === "string";
const filtered = ["1", 2, "3", 4, "5"].filter(predicate); // ['1','3','5'] string[]
```

### forEach 타입 직접 만들기

```tsx
interface Arr<T>{ // Array는 이미 만들어져있어서 안겹치게
  forEach(callback: (item: T, index: number) => void): void;
}

const a: Arr<number> = [1,2,3];
a.forEach((item, index) => {
  console.log(item, index);
  item.toFixed(1);
});
a.forEach((item) => {
  console.log(item);
  return '3';
}

const b: Arr<string> = ['1','2','3'];
b.forEach((item, index) => {
  console.log(item, index);
  item.charAt(3);
});
b.forEach((item) => {
  console.log(item);
  return '3';
}
```

### map 타입 직접 만들기

```tsx
interface Arr<T> {
  map<S>(callback: (v: T) => S): S[];
}

const a: Arr<number> = [1, 2, 3];

const b = a.map((v, i) => v + 1);
const c = a.map((v, i) => v.toString());
const d = a.map((v, i) => v % 2 === 0);

const e: Arr<string> = ["1", "2", "3"];
const f = e.map((v) => +v);
```

### filter 타입 직접 만들기

```tsx
interface Arr<T> {
  filter<S extends T>(callback: (v: T) => v is S): S[];

  filter(): void;
  filter(callback: () => void): void;
  filter(callback: (v: T) => void): T[];
  filter<S>(callback: (v: T) => v is S): S[];
  filter<S extends T>(callback: (v: T) => v is S): S[];
}

const a: Arr<number> = [1, 2, 3];

const b = a.filter((v): v is number => v % 2 === 0); // [2] number[]

const c: Arr<number | string> = [1, "2", 3, "4", 5];
const d = c.filter((v): v is string => typeof v === "string"); //['2','4'] string[]
```

### 공변성과 반공변성

함수간 대입 가능한지 불가능한지?!

```tsx
function a(x: string): number {
  return 0;
}
type B = (x: string) => number | string;
let b: B = a;
// return값이 더 넓은 함수로 대입 가능

function a(x: string): number | string {
  // (x: string) => string
  return 0;
}
type B = (x: string) => number;
let b: B = a;
// 불가능

function a(x: string | number): number {
  return 0;
}
type B = (x: string) => number;
let b: B = a;
// 매개변수는 왜 대입?!
// -> 매개변수는 (x: string | number)를 하나로 보고
// 넓은타입 -> 좁은타입 으로 대입 가능

function a(x: string): number {
  return 0;
}
type B = (x: string | number) => number;
let b: B = a;
// 얘는 매개변수가 좁은타입 -> 넓은타입이라 대입 불가능
```

### 하나에는 걸리겠지(오버로딩)

여러 함수의 선언  
죽었다 꺠어나도 함수를 하나의 선언으로 정의하기 힘들 때 여러 정의

```tsx
function add(x: number, y: number): number;
function add(x: string, y: string): string;
function add(x: any, y: any) {
  return x + y;
}

interface Add {
  (x: number, y: number): number;
  (x: string, y: string): string;
}
const add: Add = (x: any, y: any) => x + y;
```

### 타입스크립트는 건망증이 심하다(+에러 처리법)
