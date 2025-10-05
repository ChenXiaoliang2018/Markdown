## 可选属性
```typescript
interface LabelValue {
  size?: number;
  label: string
}

function printLabel(labelObj: LabelValue) {
  console.log(labelObj.label)
}

printLabel({ size: 10, label: "first label" })
printLabel({ label: "first label" })
```



# 只读属性
```typescript
interface Ponit {
  readonly x: number;
  y: number
}

let p1: Ponit = { x: 100, y: 200 }
p1.y = 300
// p1.x = 50
```



# 额外属性与索引签名
```typescript
interface Sailer {
  name: string;
  age: number;
  [propName: string]: any;
}

let s1: Sailer = { name: "chen", age: 30 }
let s2: Sailer = { name: "li", age: 20, sex: "male" }
```



# 继承接口
```typescript

interface Shape {
  color: string;
}

interface Square extends Shape {
  sideLength: number;
}

let square: Square = {
  color: "red",
  sideLength: 20
}
```



# 接口与类
```typescript
interface ClockInterface {
  currentTime: string;
  setTime(d: string): void;
}

class Clock implements ClockInterface {
  currentTime = "2024/11/12";
  setTime(d: string): void {
    this.currentTime = d
  }
}
```



