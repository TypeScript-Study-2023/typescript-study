# **Summary 🧚‍♀️**

> ts 공부를 열시미하자

# **Concept 🧐**

> 발표 주제의 이론적 개념 및 필요한 배경 지식을 설명합니다.
>
> `tsc --init` → ts code를 js code로 컴파일

`tsc --noEmit` → 단순 타입 검사

```jsx
// tsconfig.json
...
{
	"esModuleInterop": true,
	"strict": true,
	"allowJs": true, // js file 허가
	"forceConsistentCasingInFileNames": true, // 파일 이름 대소문자 구별
	"skipLibCheck": true, // 라이브러리의 *.d.ts 체크 안함
	...
}
...

```

type 관련 코드를 지워도 js 코드 문법에 맞아야함

type은 항상 최대한 정확하게 하는것이 좋다

- `const A : boolean = true;` 보다 `const A : true = true`가 더 좋은 문법
- ts가 알아서 추론을 하는데, 정확하면 바꾸지 말고 틀리게 추론한 경우 바꿀것

```jsx
const add = (a: number, b: number) : number; // js로 변환시 사라질 부분 (type 선언)
const add = (a, b) => return a+b;
```

빈 배열으로 선언하는 경우 `never[]`으로 선언이 되기 때문에 type 선언을 해야함

```jsx
const arr: string[] = [];
```

대문자 type (String) === wrapper 개체

enum type

```jsx
const enum EDirection {
	UP,
	DOWN,
	LEFT,
	RIGHT
}
// js로 컴파일시 사라짐

const ODirection = {
	UP: 0,
	DOWN: 1,
	LEFT: 2,
	RIGHT: 3,
} as const;
// js로 컴파일시 남음
```

```jsx
type A = { a: "a" } & { b: "b" };

const aa: A = {
  a: "a",
  b: "b",
};

type B = { a: "a" } | { b: "b" };

const bb: B = {
  a: "a",
  b: "b", // not needed
};
```

& → 모두 만족

| → at least one

근데 나는 interface쓸래.

넓은 타입과 좁은 타입

→ 더 규제가 많은 것이 좁은 타입

좁은 타입 → 넓은 타입

→ 인용속성검사

# **Advantages 🤩**

> 발표 주제를 적용했을 때 얻을 수 있는 이점이나 해결할 수 있는 문제 상황들에 대해 설명합니다.
> 타입에 대한 더 빡빡한 규제를 통해 예외상황에 대해 대처가능함

# **Disadvantages 🤨**

> 발표 주제를 적용했을 때 발생할 수 있는 side effect나 trade-off에 대해 설명합니다.
> typescript 자체가 type을 추측할 때 (대부분은 맞지만) 틀린 경우에는 직접 type을 고쳐주어야 한다.
> 외의 경우에는 type을 굳이 할당하지 않는 것이 더 좋음!
