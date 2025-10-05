## 数字，字符串， 布尔值
类型都是小写开头

```typescript
let isDone: boolean = true

let decLiteral: number = 6

let username: string = "Jane";
```

## null与undefined
该类型只有自身一个值

```typescript
let u: undefined;
u = undefined
// u = 4

let n: null
n = null
// n = {name: "chen"}
```



## 数组
两种书写方式

```typescript
let list1: number[] = [1, 2, 3]

let list2: Array<number> = [1, 2, 3]
```



## 元组
<font style="color:rgb(36, 36, 36);">表示一个已知元素数量和类型的数组</font>

```typescript
let x: [string, number] = ["name", 123]
```



## 枚举
表示一个有限，确定数值的集合，可以自定义映射关系

```typescript
enum Status { UNDONE = 0, DONGING = 1, FINISHED = 2 }

console.log(Status[0], Status.DONGING)
```



## Object, object与{}
Object表示任意类型

```typescript
let Ovalue: Object = 4
Ovalue = { name: 'chen' }
Ovalue = function () { }
Ovalue = "any string"
```

object表示非原始类型，

```typescript
let ovalue: object
ovalue = { age: 19 }
ovalue = function () { }
ovalue = [1, 2, 3]
// ovalue = 4
```

{}表示空对象类型，可以通过字面量来赋值，不可以通过属性设置值

```typescript
let vancantObj: {}
vancantObj = { firstname: "Jane" }
// vancantObj.x = "x"
```



## Object与any
二者都可以表示任意类型

<font style="color:rgb(36, 36, 36);">any类型会跳过检查器在编译阶段的检查，而Object不会跳过</font>

```typescript
let notSure: any;
notSure = "maybe a string"
// notSure.toFixed()     vsocode编译不会报错，在浏览器执行的时候才报错

let prettySure: Object;
prettySure = "any string"
// prettySure.toFixed(); vsocode编译提示报错
```



## void与never
<font style="color:rgb(36, 36, 36);">void表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是void</font>

```typescript
function (message: string): void { console.log(message) }
```

<font style="color:rgb(36, 36, 36);">never表示的是那些永不存在的值的类型。 比如那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型</font>

```typescript
function error(message: string): never {
  throw new Error(message)
}

function infiniteLoop(): never {
  while (true) {

  }
}
```



## 类型断言
告诉浏览器，我知道这是什么类型，常用as表示

```typescript
let someStr: any = "this is a string"

let strLength: number = (someStr as string).length
```





# 
