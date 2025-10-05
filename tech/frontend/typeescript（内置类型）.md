## Record

// B1类型由A1类型记录而来

// B1类型要求很严格，属性不能多也不能少

// A1类型中的可选属性会变成必选属性

```typescript
type A1 = {
  name: string;
  age: string;
  gender?: string;
};

type B1 = Record<keyof A1, string>;

const S1: B1 = {
  name: "CHEN",
  age: "18",
  gender: "male",
  // age: 18,
  // grade: "9",
};
```



## Partial && Required && Readonly
// 分别将类型属性变为可选，必选和只读的



```typescript
type A2 = {
  name: string;
  age: number;
};
type B2 = Partial<A2>;
type B2O = {
  [K in keyof A2]+?: A2[K];
};

type A3 = {
  name: string;
  age?: number;
};
type B3 = Required<A2>;
type B30 = {
  [K in keyof A3]-?: A3[K];
};

type A4 = {
  name: string;
  age: number;
};
type B4 = Readonly<A4>;
type B40 = {
  +readonly [K in keyof A4]: A4[K];
};
```



## Exclude && Extract
// 主要针对联合类型进行处理

// Exclude<T, U>从T中排除U中用到的类型,类似于T - U

// Extract<T, U>从T中提取U中用到的类型,类似于T ∩ U



```typescript
type A5 = "a" | "b" | "c" | "d" | "e";
type B5 = "a" | "c";
type C5 = Exclude<A5, B5>;
type C6 = Extract<A5, B5>;
```



## Pick && Omit
// 主要针对对象结构进行处理

// Pick<U, K>从U中挑选包含在联合类型U里的属性并输出

// Omit<U, K>从U中剔除包含在联合类型U里的属性并输出

```typescript
interface A6 {
  name: string;
  age: number;
  grade: number;
  gender: string;
}
type C7 = Pick<A6, "name" | "age">;
type C8 = Omit<A6, "name" | "age">;
```



## Parameters && Returntype
// 分别返回函数类型的参数类型和返回值类型



```typescript
type FN = (a: number, b: string) => string;
type P7 = Parameters<FN>;
type R7 = ReturnType<FN>;

type MyParamerters<T extends (...args: any) => any> = T extends (
  ...args: infer P
) => any
  ? P
  : never;
type P8 = MyParamerters<FN>;

type MyReturnType<T extends (...args: any) => any> = T extends (
  ...args: any
) => infer R
  ? R
  : never;
type R8 = MyReturnType<FN>;
```



