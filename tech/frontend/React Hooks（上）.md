## setState

```typescript
const [state, setState] = useState(initial);
```

### 更新状态后，立即打印状态会是新的状态还是旧的状态？

```typescript
const [count, setCount] = useState(0);
setCount(count + 1);
console.log(count); // 0
setTimeout(() => {
  console.log(count); // 0
}, 5000);
```

旧的状态，因为上下文中保存的仍然是旧的状态，新的状态得等到组件更新后才能访问。

### 多次传递下一个状态与传递更新函数的区别？

```typescript
const [count, setCount] = useState(0);
setCount(count + 1);
setCount(count + 3);
setCount(count + 2);
console.log(count); // 2
```

在多次传递下一个状态时，react 会有一个批量更新的机制，只有最后一次传入的状态生效；

```typescript
const [count, setCount] = useState(0);
setCount((count) => count + 1);
setCount((count) => count + 3);
setCount((count) => count + 2);
console.log(count); // 6
```

而多次传入更新函数，react 会维护一个队列，会记住上一次传递的新状态并且作为下一次的旧状态传入；

### 如何更新状态中的对象和数组
+ 简单对象

```typescript
const [person, setPerson] = useState({
  firstname: "John",
  lastname: "Wich",
  email: "1423143@gmail.com",
});
setPerson({
  ...person,
  email: e.target.value,
});
```

+ 嵌套对象

```typescript
const [person, setPerson] = useState({
  name: "John",
  info: {
    address: "CN",
    age: 18,
    gender: "male",
  },
});
setPerson({
  ...person,
  info: {
    ...person.info,
    address: e.target.value,
  },
});
```

+ 数组

```typescript
const [todos, setTodos] = useState([]);
// 增加
setTodos([...todos, { id: xx, content: xx, status: xx }]);
// 删除
setTodos(todos.filter((item) => item.id !== currentId));
// 修改
setTodos(
  todos.map((item) => {
    if (item.id === currentTodo.id) {
      return currentTodo;
    } else {
      return item;
    }
  })
);
```

### initial 作为参数时，传入函数的定义与传入函数执行结果的区别？
```typescript
const [todos, setTodos] = useState(createInitialTodos);
const [todos, setTodos] = useState(createInitialTodos());
```

作为函数执行结果传入时，每次状态更新重新渲染页面，会让函数重新执行，假如函数内部包含大量计算的操作，会引起性能问题，而传入一个函数的定义就只会在初始化的时候执行一次。

### key 如何重置组件的状态？
```typescript
const [version, setVersion] = useState(0);
return (
  <>
    <button onClick={() => setVersion(version + 1)}>reset</button>
    <Form key={version}></Form>
  </>
);
```

## useEffect
```typescript
useEffect(setup, dependencies?)
```

### 传递依赖数组、空数组和不传递依赖项之间的区别?
+ 空数组

组件只有在初始化的时候才执行一次，随后组件更新也不再执行，可以模拟组件挂载

```typescript
useEffect(() => {
  fetch("https://jsonplaceholder.typicode.com/todos/1")
    .then((res) => res.json())
    .then((res) => {
      console.log(res);
    });
}, []);
```

+ 传入依赖数组  
组件在初始化的时候会执行一次，然后依赖项更新，setup 函数会重新执行，可以模拟组件更新。

```typescript
function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => connection.disconnect();
  }, [roomId]);
}
```

+ 不传依赖项

不传递依赖项，组件会在初始化以及更新的时候去执行 setup 函数。

### 传递依赖项的时候，setup 与 cleanup 的执行顺序？
```typescript
useEffect(() => {
  setup();
  return () => {
    cleanup();
  };
}, [dep]);
```

+ 组件初始化的时候: setup => cleanup => setup, setup 执行两次类似于压力测试
+ 组件更新的时候: cleanup(old dep) => setup(new dep)
+ 组件卸载的时候: cleanup()

### 在什么情况下该使用 useEffect?
+ 连接到外部系统
    - 连接到聊天服务器
    - 监听全局浏览器事件
    - 触发动画效果
    - 控制模态对话框
    - 跟踪元素可见性
+ 在自定义 hook 中封装 useEffect
+ 管理非 React 的小组件
+ 使用 Effect 请求数据

### useEffect 无限循环是怎么产生的？如何解决？
产生条件:

+ 含有依赖项
+ setup 函数中修改了依赖项

```typescript
useEffect(() => {
  setCount(count + 1);
}, [count]);
```

如何解决：考虑是否需要依赖项

```typescript
useEffect(() => {
  const timer = setInterval(() => {
    setCount((count) => count + 1);
  }, 1000);
  return () => {
    clearInterval(timer);
  };
}, []);
```

### useLayoutEffect 与 useEffect 的区别？
+ useLayoutEffect 常在浏览器重新绘制屏幕之前进行布局测量，会阻塞浏览器的绘制，可能会有性能问题，所以尽量不要使用。
+ useEffect 不会阻塞浏览器的绘制，所以大多数情况下要使用它，如果屏幕出现了闪烁，可以尝试用 useLayoutEffect。

## useRef
```typescript
const ref = useRef(initialValue);
```

### useRef 初始化一个值的时候与 useState 的区别？
```typescript
const count = useRef(0);
setInterval(() => {
  count.current++;
  console.log(count.current);
}, 1000);
```

useRef 初始化的变量更改时不会触发重新渲染,而 useState 初始化的变量更改会触发重新渲染

### useRef 主要用来获取 DOM 的方式？
```typescript
const inputRef = useRef<HTMLInputElement>(null);
return (
  <>
    <input type="text" ref={inputRef} />
    <button
      onClick={() => {
        inputRef.current && inputRef.current.focus();
      }}
    >
      点击聚焦
    </button>

  </>
);
```

### useRef 如何获取自定义组件内部的 DOM?
```typescript
const inputRef = useRef<HTMLInputElement>(null);
<MyInput inputRef={inputRef}></MyInput>;

interface MyInputProps {
  inputRef: React.RefObject<HTMLInputElement>;
}
function MyInput({ inputRef }: MyInputProps) {
  return <input type="text" ref={inputRef} />;
}
```

