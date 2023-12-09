# **Summary 🧚‍♀️**

학습 범위: ts-all-in-one 강의 (섹션 3. Utility Types)

# **Concept 🧐**

### Partial 타입 분석

```tsx
type Partial<T> = {
  [P in keyof T]?: T[P];
};
// ---
interface Profile {
  name: string;
  age: number;
  married: boolean;
}
type Name = Profile["name"];

const zerocho: Profile = {
  name: "zerocho",
  age: 29,
  married: false,
};

const newZeroCho: Partial<Profile> = {
  name: "zerocho",
  age: 29,
};
```

### Pick 타입 분석

하우에버, Partial은 좋은게 아님 (모든게 optional이면 안써도 되겠네)

```tsx
interface Profile {
  name: string;
  age: number;
  married: boolean;
}
type Name = Profile["name"];

type Pick<T, S extends keyof T> {
  [P in S]: T[P];
}

const zerocho: Profile = {
  name: "zerocho",
  age: 29,
  married: false,
};

const newZeroCho: Pick<Profile 'name' | 'age'> = {
  name: "zerocho",
  age: 29,
};
```

### Omit, Exclude, Extract 타입 분석

Omit -> Pick, Exclude 사용해서 만들어냄

```tsx
type Exclude<T, U> = T extends U ? never : T;
type Extract<T, U> = T extends U ? T : never;
```

```tsx
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
```

Exclude, Extract

```tsx
type Animal = "Cat" | "Dog" | "Human";
type Mammal = Exclude<Animal, "Human">; // Mammal = 'Cat' | 'Dog';
type Human = Extract<Animal, "Human">; // Human = 'Human';
```

### Required, Record, NonNullable 타입 분석

`Required` : -? (modifier) -> optional(?)들을 다 빼버리는 것

`Readonly` : 수정 못하게 만들기  
key앞에 readonly 붙이면 됨  
readonly 빼고 가져오고싶으면 key앞에 -readonly

`Record` : Obj만드는 것

```tsx
type R<T extends keyof any, S> = {
  [key in T]: S;
};
```

`NonNullable` : Null, Undefined제외

```tsx
type A = string | null | undefined | boolean | number;
type B = N<A>;

type N<T> = T extends null | undefined ? : never : T;
```

### infer 타입 분석

```tsx
function zip(x: number, y: string, z : boolean) : { x: number, y: string, z : boolean} {
  return {x, y, z};
}

type P<T extends (...args, any) => any> T extends (...args: infer A) => any ? A : never; //<Parameters>
type R<T extends (...args, any) => any> T extends (...args: any) => infer A ? A : never; // <ReturnType>
type Params = P<typeof zip>;
type First = Params[0]
```

infer는 extends 에서만 사용 가능
