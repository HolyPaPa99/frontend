# React笔记



## 一、简介

React 是一个用于构建用户界面的 JAVASCRIPT 库。React 应用程序是由 **组件** 组成的。一个组件是 UI（用户界面）的一部分，它拥有自己的逻辑和外观。组件可以小到一个按钮，也可以大到整个页面。React具有以下特点：

- **声明式设计** −React采用声明范式，可以轻松描述应用。
- **高效** −React通过对DOM的模拟，最大限度地减少与DOM的交互。
- **灵活** −React可以与已知的库或框架很好地配合。
- **JSX** − JSX 是 JavaScript 语法的扩展。React 开发不一定使用 JSX ，但我们建议使用它。
- **组件** − 通过 React 构建组件，使得代码更加容易得到复用，能够很好的应用在大项目的开发中。
- **单向响应的数据流** − React 实现了单向响应的数据流，从而减少了重复代码，这也是它为什么比传统数据绑定更简单。

React 可以改变你对可见设计和应用构建的思考。当你使用 React 构建用户界面时，你首先会把它分解成一个个 **组件**，然后，你需要把这些组件连接在一起，使数据流经它们。

![](images/thinking-in-react.png)



## 二、JSX

React 使用 JSX 来替代常规的 JavaScript。

```jsx
const element = <h1>Hello, world!</h1>;
```

这种看起来可能有些奇怪的标签语法既不是字符串也不是 HTML。

它被称为 JSX， 一种 JavaScript 的语法扩展。 JSX 比 HTML 更加严格。你必须闭合标签，如 `<br />`。你的组件也不能返回多个 JSX 标签。你必须将它们包裹到一个共享的父级中，比如 `<div>...</div>` 或使用空的 `<>...</>` 包裹：

```jsx
function AboutPage() {
  return (
    <>
      <h1>About</h1>
      <p>Hello there.<br />How do you do?</p>
    </>
  );
}
```

如果你有大量的 HTML 需要移植到 JSX 中，你可以使用 [在线转换器](https://transform.tools/html-to-jsx)。



### 添加样式

在 React 中，你可以使用 `className` 来指定一个 CSS 的 class。它与 HTML 的 [`class`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/class) 属性的工作方式相同：

```jsx
<img className="avatar" />
```

当你的样式依赖于 JavaScript 变量时，你可以使用 `style` 属性。

```jsx
const user = {
  name: 'Hedy Lamarr',
  imageUrl: 'https://i.imgur.com/yXOvdOSs.jpg',
  imageSize: 90,
};

export default function Profile() {
  return (
    <>
      <h1>{user.name}</h1>
      <img
        className="avatar"
        src={user.imageUrl}
        alt={'Photo of ' + user.name}
        style={{
          width: user.imageSize,
          height: user.imageSize
        }}
      />
    </>
  );
}
```



### 显示数据

JSX中使用大括号`{}`嵌入Javascript代码。

```jsx
return (
  <img
    className="avatar"
    src={user.imageUrl}
  />
);
```

### 条件渲染

* `if`

  ```jsx
  let content;
  if (isLoggedIn) {
    content = <AdminPanel />;
  } else {
    content = <LoginForm />;
  }
  return (
    <div>
      {content}
    </div>
  );
  ```

  

* &&

  ```jsx
  <div>
    {isLoggedIn && <AdminPanel />}
  </div>
  ```

  

* 三目运算符

  ```jsx
  <div>
    {isLoggedIn ? (
      <AdminPanel />
    ) : (
      <LoginForm />
    )}
  </div>
  ```

  

### 渲染列表和`key`

Keys 可以在 DOM 中的某些元素被增加或删除的时候帮助 React 识别哪些元素发生了变化。因此你应当给数组中的每一个元素赋予一个确定的标识。一个元素的 key 最好是这个元素在列表中拥有的一个独一无二的字符串。通常，我们使用来自数据的 id 作为元素的 key。数组元素中使用的 key 在其兄弟之间应该是独一无二的。然而，它们不需要是全局唯一的。当我们生成两个不同的数组时，我们可以使用相同的键。

```jsx
const products = [
  { title: 'Cabbage', isFruit: false, id: 1 },
  { title: 'Garlic', isFruit: false, id: 2 },
  { title: 'Apple', isFruit: true, id: 3 },
];

export default function ShoppingList() {
  const listItems = products.map(product =>
    <li
      key={product.id}
      style={{
        color: product.isFruit ? 'magenta' : 'darkgreen'
      }}
    >
      {product.title}
    </li>
  );

  return (
    <ul>{listItems}</ul>
  );
}
```

### 响应事件

通过在组件中声明 **事件处理** 函数来响应事件：

```jsx
function MyButton() {
  function handleClick() {
    alert('You clicked me!');
  }

  return (
    <button onClick={handleClick}>
      Click me
    </button>
  );
}
```



## 三、组件

### 定义组件

* 函数式

  ```jsx
  function HelloMessage(props) {
      return <h1>Hello World!</h1>;
  }
  ```

  

* 通过类定义的面向对象方式

  ```tsx
  class Welcome extends React.Component {
    render() {
      return <h1>Hello World!</h1>;
    }
  }
  ```

  

### 导出组件

通过`export或export default`(详细用法参考ES6语法)导出组件：

```jsx
function Profile() {
  return (
    <img
      src="https://i.imgur.com/MK3eW3As.jpg"
      alt="Katherine Johnson"
    />
  );
}

export default function Gallery() {
  return (
    <section>
      <h1>了不起的科学家们</h1>
      <Profile />
      <Profile />
      <Profile />
    </section>
  );
}
```



### 导入组件

通过`import`(详细用法参考ES6语法)导入组件：

```jsx
import Gallery from './Gallery.js';

export default function App() {
  return (
    <Gallery />
  );
}
```



### props

React 组件使用 *props* 来互相通信。每个父组件都可以提供 props 给它的子组件，从而将一些信息传递给它。`props`是不可变的，当一个组件需要改变它的 props（例如，响应用户交互或新数据）时，它不得不“请求”它的父组件传递 **不同的 props** —— 一个新对象！它的旧 props 将被丢弃，最终 JavaScript 引擎将回收它们占用的内存。一个组件可以有多个`props`属性，这些 props 在 `({` 和 `})` 之间，并由逗号分隔。`props`可以设置默认值，如`function Avatar({person, size = 100})`。

```jsx
import { getImageUrl } from './utils.js';

function Avatar({ person, size = 100 }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person)}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}

export default function Profile() {
  return (
    <div>
      <Avatar
        size={100}
        person={{ 
          name: 'Katsuko Saruhashi', 
          imageId: 'YfeOqp2'
        }}
      />
      <Avatar
        size={80}
        person={{
          name: 'Aklilu Lemma', 
          imageId: 'OKS67lh'
        }}
      />
      <Avatar
        size={50}
        person={{ 
          name: 'Lin Lanying',
          imageId: '1bX5QH6'
        }}
      />
    </div>
  );
}
```

### ref

React 支持一种非常特殊的属性 **Ref** ，你可以用来绑定到 render() 输出的任何组件上。这个特殊的属性允许你引用 render() 返回的相应的支撑实例（ backing instance ）。这样就可以确保在任何时间总是拿到正确的实例。

```tsx
class MyComponent extends React.Component {
  handleClick() {
    // 使用原生的 DOM API 获取焦点
    this.refs.myInput.focus();
  }
  render() {
    //  当组件插入到 DOM 后，ref 属性添加一个组件的引用于到 this.refs
    return (
      <div>
        <input type="text" ref="myInput" />
        <input
          type="button"
          value="点我输入框获取焦点"
          onClick={this.handleClick.bind(this)}
        />
      </div>
    );
  }
}
 
ReactDOM.render(
  <MyComponent />,
  document.getElementById('example')
);
```



### 组件的生命周期

参考：https://zh-hans.legacy.reactjs.org/docs/react-component.html#reference

组件的生命周期可分成三个状态：

- Mounting(挂载)：已插入真实 DOM
- Updating(更新)：正在被重新渲染
- Unmounting(卸载)：已移出真实 DOM

每个组件都包含 “生命周期方法”，你可以重写这些方法，以便于在运行过程中特定的阶段执行这些方法。

![](images/react-lifecycle.png)

#### 挂载 

当组件实例被创建并插入 DOM 中时，其生命周期调用顺序如下：

- [**`constructor()`**](https://zh-hans.legacy.reactjs.org/docs/react-component.html#constructor)
- [`static getDerivedStateFromProps()`](https://zh-hans.legacy.reactjs.org/docs/react-component.html#static-getderivedstatefromprops)
- [**`render()`**](https://zh-hans.legacy.reactjs.org/docs/react-component.html#render)
- [**`componentDidMount()`**](https://zh-hans.legacy.reactjs.org/docs/react-component.html#componentdidmount)

#### 更新 

当组件的 props 或 state 发生变化时会触发更新。组件更新的生命周期调用顺序如下：

- [`static getDerivedStateFromProps()`](https://zh-hans.legacy.reactjs.org/docs/react-component.html#static-getderivedstatefromprops)
- [`shouldComponentUpdate()`](https://zh-hans.legacy.reactjs.org/docs/react-component.html#shouldcomponentupdate)
- [**`render()`**](https://zh-hans.legacy.reactjs.org/docs/react-component.html#render)
- [`getSnapshotBeforeUpdate()`](https://zh-hans.legacy.reactjs.org/docs/react-component.html#getsnapshotbeforeupdate)
- [**`componentDidUpdate()`**](https://zh-hans.legacy.reactjs.org/docs/react-component.html#componentdidupdate)

#### 卸载 

当组件从 DOM 中移除时会调用如下方法：

- [**`componentWillUnmount()`**](https://zh-hans.legacy.reactjs.org/docs/react-component.html#componentwillunmount)

#### 错误处理 

当渲染过程，生命周期，或子组件的构造函数中抛出错误时，会调用如下方法：

- [`static getDerivedStateFromError()`](https://zh-hans.legacy.reactjs.org/docs/react-component.html#static-getderivedstatefromerror)
- [`componentDidCatch()`](https://zh-hans.legacy.reactjs.org/docs/react-component.html#componentdidcatch)

### 

## 四、hooks

参考：https://zh-hans.react.dev/reference/react/

### state hooks

state可以让组件记录用户输入的数据。例如一个表单组件可以用state来记录用户输入，一个图片选取组件可以使用state来记录用户选择的图片。

给组件添加state有两种方法：

#### useState

`useState`方法声明一个state变量并设置一个初始值，返回一个数组对象。格式为`const [state, setState] = useState(initialState);`,使用`setState`方法可以直接修改变量值。

```jsx
import { useState } from 'react';
function MyComponent(){
  const [index, setIndex] = useState(0);
  //...
}

```



#### useReducer

`useReducer`方法给组件添加一个reducer来管理组件的state变量，返回一个数组对象。用法为`const [state, dispatch] = useReducer(reducer, initialArg, init?)`，通`过dispatch`来更改`state`的值。`useReducer`有三个参数：

* reducer 更新state的reducer函数。有两个参数：`state`（当前state值）和`action`（用户触发的操作类型）。reducer需要返回新state值。
* initialArg 初始state计算的参数，可以是任何类型。如果有`init`初始化函数参数则会作为其参数计算state的初始值，否则直接被用作state的初始值。
* init 这是一个可选参数，state初始化函数。通过调用`init(initialArg)`生成state的初始化值。

```jsx
import { useReducer } from 'react';

function reducer(state, action) {
  if (action.type === 'incremented_age') {
    return {
      age: state.age + 1
    };
  }
  throw Error('Unknown action.');
}

export default function Counter() {
  const [state, dispatch] = useReducer(reducer, { age: 42 });

  return (
    <>
      <button onClick={() => {
        dispatch({ type: 'incremented_age' })
      }}>
        Increment age
      </button>
      <p>Hello! You are {state.age}.</p>
    </>
  );
}

```

`useReducer`返回的`dispatch`函数用来触发state值的变更，有一个参数：

* action 可以是任何类型，代表用户触发的操作类型，会传递给`reducer`函数，作为`reducer`函数的第二个参数。



### context hooks





### ref hooks



### effect hooks



### performance hooks





### other hooks









## 五、Mobx







## 六、编码风格







## 七、react-router





https://cn.mobx.js.org