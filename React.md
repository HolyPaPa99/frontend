# React笔记



## 一、简介

React 是一个用于构建用户界面的 JAVASCRIPT 库。React 应用程序是由 **组件** 组成的。一个组件是 UI（用户界面）的一部分，它拥有自己的逻辑和外观。组件可以小到一个按钮，也可以大到整个页面。React具有以下特点：

- **声明式设计** −React采用声明范式，可以轻松描述应用。
- **高效** −React通过对DOM的模拟，最大限度地减少与DOM的交互。
- **灵活** −React可以与已知的库或框架很好地配合。
- **JSX** − JSX 是 JavaScript 语法的扩展。React 开发不一定使用 JSX ，但我们建议使用它。
- **组件** − 通过 React 构建组件，使得代码更加容易得到复用，能够很好的应用在大项目的开发中。
- **单向响应的数据流** − React 实现了单向响应的数据流，从而减少了重复代码，这也是它为什么比传统数据绑定更简单。



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





## 三、组件





## 四、props和state





## 五、hooks





https://react.dev/reference/react

https://react.docschina.org/learn

https://www.runoob.com/react/react-jsx.html

https://typescript.bootcss.com/tutorials/react-&-webpack.html

https://juejin.cn/post/7041551402048421901

https://cn.mobx.js.org