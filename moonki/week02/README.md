# **Summary 🧚‍♀️**

학습 범위: ts-all-in-one 강의 (void의 두가지 사용법 ~ 섹션01. 기본값 타이핑)

# **Concept 🧐**

> 발표 주제의 이론적 개념 및 필요한 배경 지식을 설명합니다.

### - Void의 두가지 사용법

함수에서 return 값을 사용하지 않는 경우의 void타입과, return 값이 void인 경우의 구분 필요!

```tsx
declare function forEach(arr: number[], callback: (el: number) => void): void;

let target: number[] = [];
forEach([1, 2, 3], (el) => target.push(el));
```

의 예제에서 확인할 수 있듯이, 매개변수에서의 void는 실제 return 값을 상관하지 않겠다는 형식으로 쓰임.  
**_-> 근데 이 부분이 잘 이해가 안감 ㅜ_**

React를 사용하는 경우에는 type 강제 변환할 때 `as` 사용하는것 추천~!

### - unknown과 any

any : type checking을 하지 않음
unknown : 지금은 모르지만, 나중에 정하겠다!

```tsx
try {
	...
} catch (error) { // -> 이때의 error는 unknown타입
	(error as Error).message
}
```

### - 타입 좁히기(타입 가드)

꿀팁 : 타입에서 error message의 마지막줄만 보면 됨!
꿀팁2 : 남이 만든 타입이 틀렸을 때 or unknown일때 빼고는 as 쓰지 않기

typeguard : if-else를 통해 type checking 해주는 것

```tsx
function numOrStr(a: number | string | number[]) {
	if (typeof a === 'string'){
		//... string ...
	} else if (typeof a === 'number'){
		//... number ...
	} else if (Array.isArray(a)){
		//... number[] ...
	} else if (typeof a === 'boolean'){
		//... never ...
	}
}

//--- class의 경우 ---
class A {
	aaa() {}
}
class B {
	bbb() {}
}

function aOrB(param: A | B) {
	if (param instance of A){
		param.aaa();
	}
}

// aOrB(A); <- error
aOrB(new A());
aOrB(new B());
```

type guard 사용해서 typescript에게 타입을 추론시킬 때, 값과, 속성 두가지 모두로 typeguard 사용 가능!

[tip] : 객체를 생성할 때 typescript를 쓴다면 type을 하나 달아주는것이 좋다! (라벨링)

### 커스텀 타입 가드(is, 형식 조건자)

-> 타입을 구분해주는 커스텀 함수를 여러분이 직접 만들 수 있어요!
return값에 is가 들어있는 함수 : custom type guard 함수!

```tsx
function catOrDOg(a: Cat | Dog): a is Dog {
	if((a as Cat).meow){return false}
	return true;
}

...
if(catOrDog(a)) { // if문 안에서 사용
	console.log(a.bow)
}
```

> ### 혼틈 Promise강의
>
> Promise는 Promise -> Pending(실행중) -> Settled(실행완료)의 단계로 구성.  
> 이 중, Settled에는 Fulfilled와 Rejected가 있음  
> [promise함수]  
> .then() <- fulfilled  
> .catch() <- rejected  
> 둘다 합쳐서 settled

```tsx
const isRejected = (
  input: PromiseSettledResult<unknown>
): input is PromiseRejectedResult => {
  return input.status === "rejected";
};
const isFulfilled = <T,>(
  input: PromiseSettledResult<T>
): input is PromiseFulfilledResult<T> => {
  return input.status === "fulfilled";
};

const promises = await Promise.allSettled([
  Promise.resolve("a"),
  Promise.resolve("b"),
]);
const errors = promises.filter((a) => true); // typeof errors === 'PromiseSettledResult[]'
const errors2 = promises.filter(isRejected); // typeof errors2 === 'PromiseRejectedResult'

export {};
```

### {}와 Object

4.8버전 update된 내용에서 새로 추가된 부분  
{}, Object는 모든 타입!(null과 undefined 제외))  
실제 객체 : object,,,지만! use interface, type, class instead.

### readonly, 인덱스드 시그니처, 맵드 타입스

```tsx
interface A {
  readonly a: string;
  b: string;
}

const aaaa: A = { a: "hello", b: "world" };
aaaa.a = "123"; // <- error!
```

type A = {a: string, b: string, c: string, d: string}  
-> indexed signature 사용  
type A = { [key: string]: string };  
type B = 'Human' | 'Mammal' | 'Animal'; <- mapped types

### 클래스의 새로운 기능들

```tsx
class A {
  a: string;
  b: number;
  constructor(a, b) {
    this.a = a;
    this.b = b;
  }
  method() {}
}

type AA = A;
const a: A = new A("123");
const b: typeof A = A;
```

private 변수 사용할 때 `private a`와 같이 사용 (`#a`도 가능)

public : 어느곳에서도 접근 가능  
private : class 내부에서는 사용가능하지만, 외부에서는 사용 불가능  
protected : class 내부에서 사용가능, 외부에서 사용 불가능! but, 상속받은 경우에는 사용 가능  
abstract : 상속받은 class에서 구현 해야 error 발생하지 않음

### 옵셔널, 제네릭 기본

속성명 뒤의 ? = 옵셔널 : 있어도 되고 없어도 됨

Problem & Solve flow

```tsx
// [1] 아래 두 호출에 대한 예외처리가 안되는 원시함수
function add(x: string | number, y: string | number): string | number {
  return x + y;
}
add(1, 2); // 3
add("1", "2"); // '12'

add(1, "2"); // '12'
add("1", 2); // '12'

// [2] 함수를 둘로 나눔. but, 함수의 중복호출 발생
function add(x: string, y: string): string {
  return x + y;
}
function add(x: number, y: number): number {
  return x + y;
}
add(1, 2);
add("1", "2");

// [3] body를 없애서 type선언으로 바꿈. but, 실제 구현 한번 해야함
function add(x: string, y: string): string;
function add(x: number, y: number): number;
function add(x: string | number, y: string | number) {
  return x + y;
}
add(1, "2"); // '12'
add("1", 2); // '12' -> 다시 이거 처리 안됨

// [4] Generic -> type을 변수처럼 사용
function add<T>(x: T, y: T): T {
  return x + y;
}
add(1, 2); // 3
add("1", "2"); // '12'

add(1, "2"); // error
add("1", 2); // error
add(true, false); // non-error!!!

// [5] 제너릭에 타입 추가
function add<T extends number | string>(x: T, y: T): T {
  return x + y;
}
add(1, 2); // 3
add("1", "2"); // '12'

add(1, "2"); // error
add("1", 2); // error
add(true, false); // error
```

### 기본값 타이핑

Ts에서 기본값을 통해 type을 기본적으로 추론을 하는데, 위치 유의  
type은 항상 값 바로 뒤에 위치

React에서 typescript 사용할 때 제네릭 사용하려면 기본값 할당해주면 됨

```tsx
const add1 = <T extends unknown>(x: T, y: T) => ({ x, y });
const add2 = <T = unknown,>(x: T, y: T) => ({ x, y });
const add3 = <T,>(x: T, y: T) => ({ x, y });
```

1. extends ~
2. = 기본값
3. 제네릭 뒤에 콤마 하나 -> 의도가 잘 드러나지 않아...그치만 간지나....
