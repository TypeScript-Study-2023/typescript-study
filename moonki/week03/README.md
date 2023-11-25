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

https://www.inflearn.com/course/lecture?courseSlug=%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%98%AC%EC%9D%B8%EC%9B%90-1&unitId=122325  
9:00ㅇ ㅣ어보기
