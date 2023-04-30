

# ES6 笔记

参考：https://www.bookstack.cn/read/es6-3rd/



## 一、变量、常量和顶层对象

保留`var` 、`function`声明全局变量关键字。

`let`声明变量

* 所声明的变量，只在`let`命令所在的代码块内有效。
* 不存在变量提升，它所声明的变量一定要在声明后使用，否则报错。
* 不允许在相同作用域内，重复声明同一个变量。

`const`声明常量

* `const`声明一个只读的常量。一旦声明，常量的值就不能改变。
* `const`一旦声明变量，就必须立即初始化，不能留到以后赋值。
* 只在声明所在的块级作用域内有效。
* `const`命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。

`this`替代顶层对象`window`

- 全局环境中，`this`会返回顶层对象。但是，Node 模块和 ES6 模块中，`this`返回的是当前模块。
- 函数里面的`this`，如果函数不是作为对象的方法运行，而是单纯作为函数运行，`this`会指向顶层对象。但是，严格模式下，这时`this`会返回`undefined`。
- 不管是严格模式，还是普通模式，`new Function('return this')()`，总是会返回全局对象。但是，如果浏览器用了 CSP（Content Security Policy，内容安全策略），那么`eval`、`new Function`这些方法都可能无法使用。

ES2020 在语言标准的层面，引入`globalThis`作为顶层对象。也就是说，任何环境下，`globalThis`都是存在的，都可以从它拿到顶层对象，指向全局环境下的`this`。

垫片库[`global-this`](https://github.com/ungap/global-this)模拟了这个提案，可以在所有环境拿到`globalThis`。



## 二、变量解构赋值

### 1. 数组解构

ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构（Destructuring）。

```js
let [a, b, c] = [1, 2, 3];
```

* 如果解构不成功，变量的值就等于`undefined`。

  ```js
  let [foo, [[bar], baz]] = [1, [[2], 3]];
  foo // 1
  bar // 2
  baz // 3
  let [ , , third] = ["foo", "bar", "baz"];
  third // "baz"
  let [x, , y] = [1, 2, 3];
  x // 1
  y // 3
  let [head, ...tail] = [1, 2, 3, 4];
  head // 1
  tail // [2, 3, 4]
  let [x, y, ...z] = ['a'];
  x // "a"
  y // undefined
  z // []
  ```

* 如果等号的右边不是数组（或者严格地说，不是可遍历的结构），那么将会报错。

  ```js
  // 报错
  let [foo] = 1;
  let [foo] = false;
  let [foo] = NaN;
  let [foo] = undefined;
  let [foo] = null;
  let [foo] = {};
  ```

  事实上，只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。

  ```js
  let [x, y, z] = new Set(['a', 'b', 'c']);
  x // "a"
  ```

  ```js
  function* fibs() {
    let a = 0;
    let b = 1;
    while (true) {
      yield a;
      [a, b] = [b, a + b];
    }
  }
  let [first, second, third, fourth, fifth, sixth] = fibs();
  sixth // 5
  ```

  `fibs`是一个 Generator 函数，原生具有 Iterator 接口。解构赋值会依次从这个接口获取值。

* 解构赋值允许指定默认值。

  ES6 内部使用严格相等运算符（`===`），判断一个位置是否有值。所以，只有当一个数组成员严格等于`undefined`，默认值才会生效。如果默认值是一个表达式，那么这个表达式是惰性求值的，即只有在用到的时候，才会求值。

  ```js
  let [foo = true] = [];
  foo // true
  let [x, y = 'b'] = ['a']; // x='a', y='b'
  let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
  //如果一个数组成员是null，默认值就不会生效，因为null不严格等于undefined。
  let [x = 1] = [undefined];
  x // 1
  let [x = 1] = [null];
  x // null
  
  function f() {
    console.log('aaa');
  }
  let [x = f()] = [1];
  ```

  默认值可以引用解构赋值的其他变量，但该变量必须已经声明。

  ```js
  let [x = 1, y = x] = [];     // x=1; y=1
  let [x = 1, y = x] = [2];    // x=2; y=2
  let [x = 1, y = x] = [1, 2]; // x=1; y=2
  let [x = y, y = 1] = [];     // ReferenceError: y is not defined
  //因为x用y做默认值时，y还没有声明
  ```

  

### 2.对象解构



