## 类的形式

这个类有3个成员：一个叫做 greeting的属性，一个构造函数和一个 greet方法。

```typescript
class Greeter {
  greeting: string;
  constructor(message: string) {
    this.greeting = message
  }

  greet() {
    console.log("Hello " + this.greeting)
  }
}
```

调用之前定义的构造函数，创建一个 Greeter类型的新对象，并执行构造函数初始化它。

```typescript
let greeter = new Greeter("World !")
greeter.greet()
```

## 类的继承
super会执行基类的构造函数。 在构造函数里访问this之前，一定要先调用 super()

```typescript
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name
  }

  move(meters: number = 0) {
    console.log(`${this.name} moved ${meters} meters!`)
  }
}

class Dog extends Animal {
  constructor(name: string) {
    super(name)
    // this.name = name
  }
  move(meters: number = 5): void {
    super.move(meters)
    // console.log(`${this.name} + "moved" + ${meters}`)
  }
  bark() {
    console.log("woof!woof!woof!")
  }
}
```

Dog是一个派生类，它派生自Animal基类，通过extends关键字继承。

派生类通常被称作子类，基类通常被称作超类。

```typescript
let dog = new Dog("feibiao")
dog.move()
dog.bark()
```

## public
在TypeScript里，成员都默认为public。

```typescript
class Student {
  public constructor(public name: string) {
    this.name = name
  }

  public sayHello() {
    console.log("Hello")
  }
}
```

## private
<font style="color:rgb(36, 36, 36);">当成员被标记成 private时，它就不能在声明它的类的外部访问,在子类中也不可以访问。</font>

```typescript
class Male {
  public name;
  private gender;
  constructor(name: string, gender: string) {
    this.gender = gender
    this.name = name
  }

  sayGender() {
    console.log(this.name + " gender is " + this.gender)
  }
}

const male = new Male("chen", "helicopter")
male.sayGender()
console.log(male.name)
// console.log(male.gender)

class AisanMale extends Male {
  constructor(name: string, gender: string) {
    super(name, gender)
  }
  sayGender(): void {
    super.sayGender()
  }
}

const asian_male = new AisanMale("li", "plastic bags")
asian_male.sayGender()
console.log(asian_male.name)
// console.log(asian_male.gender)
```

## protected
protected修饰符与private修饰符的行为相似，但protected成员在派生类中仍然可以访问。

## readonly
可以使用 readonly关键字将属性设置为只读的

```typescript
class Test1 {
  readonly name: string;
  constructor(name: string) {
    this.name = name
  }
}

let t1 = new Test1("chen")
// t1.name = "cc"
```

## 参数属性
仅在构造函数里使用readonlyname:string参数来创建和初始化name成员。我们把声明和赋值合并至一处，public与private也是同样

```typescript
class Test2 {
  constructor(readonly name: string) {
    this.name = name
  }
}
```

## 寄存器与静态属性
TypeScript支持通过getters/setters来截取对对象成员的访问。 

静态属性存在于类本身上面而不是类的实例上

```typescript
class Employee {
    static username = "chen"
    static password = "123456"
    constructor(private _username: string, private _password: string) {

    }

    get isAuthorized() {
        if (this._password === Employee.password && this._username === Employee.username) {
            return "Authorized"
        } else {
            return 'Unauthorized'
        }
    }

    set password(newPassword: string) {
        Employee.password = newPassword
    }
    set username(newUsername: string) {
        Employee.username = newUsername
    }
}

let e1 = new Employee("LI", "admin")
let e2 = new Employee("chen", "123456")

console.log(e1.isAuthorized)
console.log(e2.isAuthorized)

e1.username = "LI"
e1.password = "admin"


console.log(Employee.username, Employee.password)
console.log(e1.isAuthorized)
console.log(e2.isAuthorized)
```

## 抽象类与抽象方法
抽象类做为其它派生类的基类使用。 它们一般不会直接被实例化。

抽象类中的抽象方法不包含具体实现并且必须在派生类中实现。 

```typescript
bstract class Department {

    constructor(public name: string) {
    }

    printName(): void {
        console.log('Department name: ' + this.name);
    }

    abstract printMeeting(): void; // 必须在派生类中实现
}

class AccountingDepartment extends Department {

    constructor() {
        super('Accounting and Auditing'); // 在派生类的构造函数中必须调用 super()
    }

    printMeeting(): void {
        console.log('The Accounting Department meets each Monday at 10am.');
    }

    generateReports(): void {
        console.log('Generating accounting reports...');
    }
}

let department: Department; // 允许创建一个对抽象类型的引用
// department = new Department(); // 错误: 不能创建一个抽象类的实例
department = new AccountingDepartment(); // 允许对一个抽象子类进行实例化和赋值
department.printName();
department.printMeeting();
// department.generateReports(); // 错误: 方法在声明的抽象类中不存在
```

