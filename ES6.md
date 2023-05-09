

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
//普通数组
let [a, b, c] = [1, 2, 3];
//嵌套数组
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3
//数组空位写法
let [ , , third] = ["foo", "bar", "baz"];
third // "baz"
let [x, , y] = [1, 2, 3];
x // 1
y // 3
//扩展运算符写法
let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]
```

* 如果解构不成功，变量的值就等于`undefined`。

  ```js
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

* 字符串也可以解构赋值。这是因为此时，字符串被转换成了一个类似数组的对象。

  ```js
  const [a, b, c, d, e] = 'hello';
  a // "h"
  b // "e"
  c // "l"
  d // "l"
  e // "o"
  let {length : len} = 'hello';
  len // 5
  ```

  

### 2.对象解构

解构不仅可以用于数组，还可以用于对象。对象解构可以更加便捷的获取对象的属性和方法。

* 对象的属性没有次序，变量必须与属性同名，才能取到正确的值。

  ```js
  let { bar, foo } = { foo: 'aaa', bar: 'bbb' };
  foo // "aaa"
  bar // "bbb"
  ```

  

* 如果解构失败，变量的值等于`undefined`。

  ```js
  let { baz } = { foo: 'aaa', bar: 'bbb' };
  baz // undefined
  ```

  

* 变量名和属性名不同的解构写法

  ```js
  let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
  baz // "aaa"
  let obj = { first: 'hello', last: 'world' };
  let { first: f, last: l } = obj;
  f // 'hello'
  l // 'world'
  ```

  

* 与数组一样也可以用于嵌套结构的对象

  ```js
  let obj = {};
  let arr = [];
  ({ foo: obj.prop, bar: arr[0] } = { foo: 123, bar: true });
  obj // {prop:123}
  arr // [true]
  
  // 报错,如果解构模式是嵌套的对象，而且子对象所在的父属性不存在，那么将会报错。
  let {foo: {bar}} = {baz: 'baz'};
  ```

  

* 对象的解构赋值可以取到继承的属性

  ```js
  const obj1 = {};
  const obj2 = { foo: 'bar' };
  Object.setPrototypeOf(obj1, obj2);
  const { foo } = obj1;
  foo // "bar"
  ```

  

* 对象的解构也可以指定默认值。默认值生效的条件是，对象的属性值严格等于`undefined`。

  ```js
  var { message: msg = 'Something went wrong' } = {};
  msg // "Something went wrong"
  ```

  

* 如果要将一个已经声明的变量用于解构赋值，必须放在一个圆括号里面，避免 JavaScript 将其解释为代码块。

  ```js
  // 正确的写法
  let x;
  ({x} = {x: 1});
  ```

  

* 由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构。

  ```js
  let arr = [1, 2, 3];
  let {0 : first, [arr.length - 1] : last} = arr;
  first // 1
  last // 3
  ```

  

数值和布尔值的包装对象都有`toString`属性，因此变量`s`都能取到值。

```js
let {toString: s} = 123;
s === Number.prototype.toString // true
let {toString: s} = true;
s === Boolean.prototype.toString // true
```



```js
let { prop: x } = undefined; // TypeError
let { prop: y } = null; // TypeError
```

解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于`undefined`和`null`无法转为对象，所以对它们进行解构赋值，都会报错。



### 3.函数参数的解构赋值

```js
function add([x, y]){
  return x + y;
}
add([1, 2]); // 3
```

上面代码中，函数`add`的参数表面上是一个数组，但在传入参数的那一刻，数组参数就被解构成变量`x`和`y`。对于函数内部的代码来说，它们能感受到的参数就是`x`和`y`。

```js
function move({x = 0, y = 0} = {}) {
  return [x, y];
}
move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]
```

上面代码中，函数`move`的参数是一个对象，通过对这个对象进行解构，得到变量`x`和`y`的值。如果解构失败，`x`和`y`等于默认值。

```js
function move({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}
move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, undefined]
move({}); // [undefined, undefined]
move(); // [0, 0]
```

```js
[1, undefined, 3].map((x = 'yes') => x);
// [ 1, 'yes', 3 ]
```



### 4.用途

* 交换变量的值

  ```js
  let x = 1;
  let y = 2;
  [x, y] = [y, x];
  ```

  

* 解构函数返回值结果

  ```js
  // 返回一个数组
  function example() {
    return [1, 2, 3];
  }
  let [a, b, c] = example();
  // 返回一个对象
  function example() {
    return {
      foo: 1,
      bar: 2
    };
  }
  let { foo, bar } = example();
  ```

  

* 函数参数定义

  ```js
  // 参数是一组有次序的值
  function f([x, y, z]) { ... }
  f([1, 2, 3]);
  // 参数是一组无次序的值
  function f({x, y, z}) { ... }
  f({z: 3, y: 2, x: 1});
  //参数默认值
  function f({x, y = 0, z}) { ... }
  f({z:3, x = 1});
  y//y = 0
  ```

  

* 提取JSON数据

  ```js
  let jsonData = {
    id: 42,
    status: "OK",
    data: [867, 5309]
  };
  let { id, status, data: number } = jsonData;
  console.log(id, status, number);
  // 42, "OK", [867, 5309]
  ```

  

* 遍历 Map 结构

  ```js
  const map = new Map();
  map.set('first', 'hello');
  map.set('second', 'world');
  for (let [key, value] of map) {
    console.log(key + " is " + value);
  }
  // first is hello
  // second is world
  
  // 获取键名
  for (let [key] of map) {
    // ...
  }
  // 获取键值
  for (let [,value] of map) {
    // ...
  }
  ```

  

* 加载模块的指定方法

  ```js
  const { SourceMapConsumer, SourceNode } = require("source-map");
  ```

  

## 三、字符串的扩展

### 1.字符的 Unicode 表示法

ES6 加强了对 Unicode 的支持，允许采用`\uxxxx`形式表示一个字符，其中`xxxx`表示字符的 Unicode 码点。这种表示法只限于码点在`\u0000`~`\uFFFF`之间的字符。超出这个范围的字符，必须用两个双字节的形式表示或将码点放入大括号`{}`。

```js
'\z' === 'z'  // true
'\172' === 'z' // true
'\x7A' === 'z' // true
'\u007A' === 'z' // true
'\u{7A}' === 'z' // true

//双字节表示法
"\uD842\uDFB7"
// "𠮷"
```



### 2.模板字符串

ES6 引入了模板字符串。模板字符串（template string）是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。

```js
$('#result').append(`
  There are <b>${basket.count}</b> items
   in your basket, <em>${basket.onSale}</em>
  are on sale!
`);

$('#list').html(`
<ul>
  <li>first</li>
  <li>second</li>
</ul>
`);
```



## 四、正则表达式

```js
var regex = new RegExp('xyz', 'i');
// 等价于
var regex = new RegExp(/xyz/i);
// 等价于
var regex = /xyz/i;
//原有正则对象的修饰符是ig，它会被第二个参数i覆盖。
new RegExp(/abc/ig, 'i').flags
```



## 五、函数

ES6 允许为函数的参数设置默认值，即直接写在参数定义的后面。

```js
function Point(x = 0, y = 0) {
  this.x = x;
  this.y = y;
}
const p = new Point();
p // { x: 0, y: 0 }
```



ES6 引入 rest 参数（形式为`...变量名`），用于获取函数的多余参数，这样就不需要使用`arguments`对象了。rest 参数搭配的变量是一个数组，该变量将多余的参数放入数组中。

```js
function add(...values) {
  let sum = 0;
  for (var val of values) {
    sum += val;
  }
  return sum;
}
add(2, 5, 3) // 10
```



从 ES5 开始，函数内部可以设定为严格模式。ES2016 做了一点修改，规定只要函数参数使用了默认值、解构赋值、或者扩展运算符，那么函数内部就不能显式设定为严格模式，否则会报错。

```js
function doSomething(a, b) {
  'use strict';
  // code
}

// 报错
function doSomething(a, b = a) {
  'use strict';
  // code
}
```

函数的`name`属性，返回该函数的函数名。

```js
function foo() {}
foo.name // "foo"
var f = function () {};
// ES5
f.name // ""
// ES6
f.name // "f"
const bar = function baz() {};
// ES5
bar.name // "baz"
// ES6
bar.name // "baz"

(new Function).name // "anonymous"

//bind返回的函数，name属性值会加上bound前缀。
function foo() {};
foo.bind({}).name // "bound foo"
(function(){}).bind({}).name // "bound "
```



ES6 允许使用“箭头”（`=>`）定义函数。

```js
var f = v => v;
// 等同于
var f = function (v) {
  return v;
};

var f = () => 5;
// 等同于
var f = function () { return 5 };
var sum = (num1, num2) => num1 + num2;
// 等同于
var sum = function(num1, num2) {
  return num1 + num2;
};


//如果箭头函数只有一行语句，且不需要返回值，可以采用下面的写法，就不用写大括号了。
let fn = () => void doesNotReturn();
```



## 六、扩展运算符

扩展运算符（spread）是三个点（`...`）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列。

```js
console.log(...[1, 2, 3])
// 1 2 3
console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5
[...document.querySelectorAll('div')]
// [<div>, <div>, <div>]
```

由于扩展运算符可以展开数组，所以不再需要`apply`方法，将数组转为函数的参数了。

```js
 // ES5 的写法
function f(x, y, z) {
  // ...
}
var args = [0, 1, 2];
f.apply(null, args);
// ES6的写法
function f(x, y, z) {
  // ...
}
let args = [0, 1, 2];
f(...args);
```

用途：

* 复制数组

  ```js
  const a1 = [1, 2];
  // 写法一
  const a2 = [...a1];
  // 写法二
  const [...a2] = a1;
  ```

  

* 合并数组

  ```js
  const arr1 = ['a', 'b'];
  const arr2 = ['c'];
  const arr3 = ['d', 'e'];
  // ES5 的合并数组
  arr1.concat(arr2, arr3);
  // [ 'a', 'b', 'c', 'd', 'e' ]
  // ES6 的合并数组
  [...arr1, ...arr2, ...arr3]
  // [ 'a', 'b', 'c', 'd', 'e' ]
  ```

  

* 数组解构赋值

  ```js
  // ES5
  a = list[0], rest = list.slice(1)
  // ES6
  [a, ...rest] = list
  //如果将扩展运算符用于数组赋值，只能放在参数的最后一位，否则会报错。
  const [...butLast, last] = [1, 2, 3, 4, 5];
  // 报错
  const [first, ...middle, last] = [1, 2, 3, 4, 5];
  // 报错
  ```

  

* 字符串处理

  ```js
  //字符串转数组
  [...'hello']
  // [ "h", "e", "l", "l", "o" ]
  //识别unicode字符，JavaScript 会将四个字节的 Unicode 字符，识别为 2 个字符，采用扩展运算符就没有这个问题。
  'x\uD83D\uDE80y'.length // 4
  [...'x\uD83D\uDE80y'].length // 3
  ```

  

* 遍历实现Iterator接口的对象

  ```js
  let nodeList = document.querySelectorAll('div');
  let array = [...nodeList];
  
  Number.prototype[Symbol.iterator] = function*() {
    let i = 0;
    let num = this.valueOf();
    while (i < num) {
      //yield 关键字使生成器函数执行暂停，yield 关键字后面的表达式的值返回给生成器的调用者。
      //它可以被认为是一个基于生成器的版本的 return 关键字。
      yield i++;
    }
  }
  console.log([...5]) // [0, 1, 2, 3, 4]
  
  let map = new Map([
    [1, 'one'],
    [2, 'two'],
    [3, 'three'],
  ]);
  let arr = [...map.keys()]; // [1, 2, 3]
  ```

  

## 七、数组空位

数组的空位指，数组的某一个位置没有任何值。比如，`Array`构造函数返回的数组都是空位。

```js
Array(3) // [, , ,]
```

空位不是`undefined`，一个位置的值等于`undefined`，依然是有值的。空位是没有任何值，`in`运算符可以说明这一点。

```js
0 in [undefined, undefined, undefined] // true
0 in [, , ,] // false
```

ES6 则是明确将空位转为`undefined`。

```js
Array.from(['a',,'b'])
// [ "a", undefined, "b" ]

//扩展运算符（...）也会将空位转为undefined。
[...['a',,'b']]
// [ "a", undefined, "b" ]

//for...of循环也会遍历空位
let arr = [, ,];
for (let i of arr) {
  console.log(1);
}
// 1
// 1
```





## 八、对象的扩展

### 1.对象的简写

ES6 允许在大括号里面，直接写入变量和函数，作为对象的属性和方法。这样的书写更加简洁。

```js
const foo = 'bar';
const baz = {foo};
baz // {foo: "bar"}
// 等同于
const baz = {foo: foo};
```

```js
const o = {
  method() {
    return "Hello!";
  }
};
// 等同于
const o = {
  method: function() {
    return "Hello!";
  }
};
```



### 2.属性名表达式

JavaScript 定义对象的属性，有两种方法。

```js
// 方法一
obj.foo = true;
// 方法二
obj['a' + 'bc'] = 123;
```

ES6 允许字面量定义对象时，用方法二（表达式）作为对象的属性名，即把表达式放在方括号内。

```js
let lastWord = 'last word';
const a = {
  'first word': 'hello',
  [lastWord]: 'world'
};
a['first word'] // "hello"
a[lastWord] // "world"
a['last word'] // "world"
```



### 3.方法的`name`属性

函数的`name`属性，返回函数名。对象方法也是函数，因此也有`name`属性。

```js
const person = {
  sayName() {
    console.log('hello!');
  },
};
person.sayName.name   // "sayName"
```



### 4.`super`关键字

ES6 又新增了另一个类似的关键字`super`，指向当前对象的原型对象。

```js
const proto = {
  foo: 'hello'
};
const obj = {
  foo: 'world',
  find() {
    return super.foo;
  }
};
Object.setPrototypeOf(obj, proto);
obj.find() // "hello"
```

**注意，`super`关键字表示原型对象时，只能用在对象的方法之中，用在其他地方都会报错。**

JavaScript 引擎内部，`super.foo`等同于`Object.getPrototypeOf(this).foo`（属性）或`Object.getPrototypeOf(this).foo.call(this)`（方法）。



## 九、`Symbol`

ES6 引入了一种新的原始数据类型`Symbol`，表示独一无二的值。

```js
let s = Symbol();
typeof s
// "symbol"
```



`Symbol`的使用：

* 作为属性名

  由于每一个 Symbol 值都是不相等的，这意味着 Symbol 值可以作为标识符，用于对象的属性名，就能保证不会出现同名的属性。这对于一个对象由多个模块构成的情况非常有用，能防止某一个键被不小心改写或覆盖。

  ```js
  let mySymbol = Symbol();
  // 第一种写法
  let a = {};
  a[mySymbol] = 'Hello!';
  // 第二种写法
  let a = {
    [mySymbol]: 'Hello!'
  };
  // 第三种写法
  let a = {};
  Object.defineProperty(a, mySymbol, { value: 'Hello!' });
  // 以上写法都得到同样结果
  a[mySymbol] // "Hello!"
  ```

  注意，Symbol 值作为对象属性名时，不能用点运算符。

  在对象的内部，使用 Symbol 值定义属性时，Symbol 值必须放在方括号之中。

  ```js
  let s = Symbol();
  let obj = {
    [s]: function (arg) { ... }
  };
  obj[s](123);
  ```

  

* 类型定义

  ```js
  const shapeType = {
    triangle: Symbol() //取代字符串形式更加严谨triangle: 'Triangle'
  };
  
  function getArea(shape, options) {
    let area = 0;
    switch (shape) {
      case shapeType.triangle:
        area = .5 * options.width * options.height;
        break;
    }
    return area;
  }
  getArea(shapeType.triangle, { width: 100, height: 100 });
  ```

  ```js
  const log = {};
  log.levels = {
    DEBUG: Symbol('debug'),
    INFO: Symbol('info'),
    WARN: Symbol('warn')
  };
  console.log(log.levels.DEBUG, 'debug message');
  console.log(log.levels.INFO, 'info message');
  ```

  

* 模块单例模式

  防止对象在其地方被重新生成造成失真。

  ```js
  // mod.js
  const FOO_KEY = Symbol.for('foo');
  function A() {
    this.foo = 'hello';
  }
  if (!global[FOO_KEY]) {
    global[FOO_KEY] = new A();
  }
  module.exports = global[FOO_KEY];
  ```

  

## 十、`Set`和`Map`数据结构

### 1.Set

ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。

```js
const s = new Set();
[2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));
for (let i of s) {
  console.log(i);
}
// 2 3 5 4
//结果表明 Set 结构不会添加重复的值

// 去除数组的重复成员
[...new Set(array)]
```



### 2.WeakSet

WeakSet 结构与 Set 类似，也是不重复的值的集合。但是，WeakSet 的成员只能是对象，而不能是其他类型的值。

```js
const ws = new WeakSet();
ws.add(1)
// TypeError: Invalid value used in weak set
ws.add(Symbol())
// TypeError: invalid value used in weak set
```

其次，WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中。WeakSet 适合临时存放一组对象，以及存放跟对象绑定的信息。只要这些对象在外部消失，它在 WeakSet 里面的引用就会自动消失。

由于 WeakSet 内部有多少个成员，取决于垃圾回收机制有没有运行，运行前后很可能成员个数是不一样的，而垃圾回收机制何时运行是不可预测的，因此 ES6 规定 WeakSet 不可遍历。

```js
const ws = new WeakSet();
const obj = {};
const foo = {};
ws.add(window);
ws.add(obj);
ws.has(window); // true
ws.has(foo);    // false
ws.delete(window);
ws.has(window);    // false
```



### 3.Map

JavaScript 的对象（Object），本质上是键值对的集合（Hash 结构），但是传统上只能用字符串当作键。这给它的使用带来了很大的限制。ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。

```js
const m = new Map();
const o = {p: 'Hello World'};
m.set(o, 'content')
m.get(o) // "content"
m.has(o) // true
m.delete(o) // true
m.has(o) // false
```

作为构造函数，Map 也可以接受一个数组作为参数。该数组的成员是一个个表示键值对的数组。事实上，不仅仅是数组，任何具有 Iterator 接口、且每个成员都是一个双元素的数组的数据结构都可以当作`Map`构造函数的参数。

```js
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);
map.size // 2
map.has('name') // true
map.get('name') // "张三"
map.has('title') // true
map.get('title') // "Author"

//Set
const set = new Set([
  ['foo', 1],
  ['bar', 2]
]);
const m1 = new Map(set);
m1.get('foo') // 1
//Map
const m2 = new Map([['baz', 3]]);
const m3 = new Map(m2);
m3.get('baz') // 3
```

注意，只有对同一个对象的引用，Map 结构才将其视为同一个键。这一点要非常小心。

```js
const map = new Map();
map.set(['a'], 555);
map.get(['a']) // undefined
```



### 4.WeakMap

`WeakMap`结构与`Map`结构类似，也是用于生成键值对的集合。`WeakMap`只接受对象作为键名（`null`除外），不接受其他类型的值作为键名。`WeakMap`的键名所指向的对象，不计入垃圾回收机制。因此，只要所引用的对象的其他引用都被清除，垃圾回收机制就会释放该对象所占用的内存。也就是说，一旦不再需要，WeakMap 里面的键名对象和所对应的键值对会自动消失，不用手动删除引用。

```js
const wm = new WeakMap();
const element = document.getElementById('example');
wm.set(element, 'some information');
wm.get(element) // "some information"
```

总之，`WeakMap`的专用场合就是，它的键所对应的对象，可能会在将来消失。`WeakMap`结构有助于防止内存泄漏。



## 十一、`Proxy`& `Reflect`

`Reflect`和`Proxy`是 ES6 为了操作对象而提供的新 API。

### 1.Proxy

在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

ES6 原生提供 Proxy 构造函数，用来生成 Proxy 实例。

```js
var proxy = new Proxy(target, handler);
```

`Proxy`构造函数接受两个参数。第一个参数`target`是所要代理的目标对象（上例是一个空对象），即如果没有`Proxy`的介入，操作原来要访问的就是这个对象；第二个参数`handler`是一个配置对象，对于每一个被代理的操作，需要提供一个对应的处理函数，该函数将拦截对应的操作。如果`handler`是一个空对象，没有任何拦截效果，访问`proxy`就等同于访问`target`。

```js
var handler = {
  get: function(target, name) {
    if (name === 'prototype') {
      return Object.prototype;
    }
    return 'Hello, ' + name;
  },
  apply: function(target, thisBinding, args) {
    return args[0];
  },
  construct: function(target, args) {
    return {value: args[1]};
  }
};
var fproxy = new Proxy(function(x, y) {
  return x + y;
}, handler);
fproxy(1, 2) // 1
new fproxy(1, 2) // {value: 2}
fproxy.prototype === Object.prototype // true
fproxy.foo === "Hello, foo" // true
```



`Proxy`一共支持13种拦截操作：

| 操作                                          | 说明                                                         |
| --------------------------------------------- | ------------------------------------------------------------ |
| **get(target, propKey, receiver)**            | 拦截对象属性的读取，比如`proxy.foo`和`proxy['foo']`。        |
| **set(target, propKey, value, receiver)**     | 拦截对象属性的设置，比如`proxy.foo = v`或`proxy['foo'] = v`，返回一个布尔值。 |
| **has(target, propKey)**                      | 拦截`propKey in proxy`的操作，返回一个布尔值。               |
| **deleteProperty(target, propKey)**           | 拦截`delete proxy[propKey]`的操作，返回一个布尔值。          |
| **ownKeys(target)**                           | 拦截`Object.getOwnPropertyNames(proxy)`、`Object.getOwnPropertySymbols(proxy)`、`Object.keys(proxy)`、`for...in`循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而`Object.keys()`的返回结果仅包括目标对象自身的可遍历属性。 |
| **getOwnPropertyDescriptor(target, propKey)** | 拦截`Object.getOwnPropertyDescriptor(proxy, propKey)`，返回属性的描述对象。 |
| **defineProperty(target, propKey, propDesc)** | 拦截`Object.defineProperty(proxy, propKey, propDesc）`、`Object.defineProperties(proxy, propDescs)`，返回一个布尔值。 |
| **preventExtensions(target)**                 | 拦截`Object.preventExtensions(proxy)`，返回一个布尔值。      |
| **getPrototypeOf(target)**                    | 拦截`Object.getPrototypeOf(proxy)`，返回一个对象。           |
| **isExtensible(target)**          | 拦截`Object.isExtensible(proxy)`，返回一个布尔值。           |
| **setPrototypeOf(target, proto)** | 拦截`Object.setPrototypeOf(proxy, proto)`，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。 |
| **apply(target, object, args)**   | 拦截 Proxy 实例作为函数调用的操作，比如`proxy(...args)`、`proxy.call(object, ...args)`、`proxy.apply(...)`。 |
| **construct(target, args)**       | 拦截 Proxy 实例作为构造函数调用的操作，比如`new proxy(...args)`。 |



**`Proxy.revocable()`方法返回一个可取消的 Proxy 实例。**

```js
let target = {};
let handler = {};
let {proxy, revoke} = Proxy.revocable(target, handler);
proxy.foo = 123;
proxy.foo // 123
revoke();
proxy.foo // TypeError: Revoked
```

`Proxy.revocable()`方法返回一个对象，该对象的`proxy`属性是`Proxy`实例，`revoke`属性是一个函数，可以取消`Proxy`实例。上面代码中，当执行`revoke`函数之后，再访问`Proxy`实例，就会抛出一个错误。

**在 Proxy 代理的情况下，目标对象内部的`this`关键字会指向 Proxy 代理。**

```js
const target = {
  m: function () {
    console.log(this === proxy);
  }
};
const handler = {};
const proxy = new Proxy(target, handler);
target.m() // false
proxy.m()  // true
```

```js
const _name = new WeakMap();
class Person {
  constructor(name) {
    _name.set(this, name);
  }
  get name() {
    return _name.get(this);
  }
}
const jane = new Person('Jane');
jane.name // 'Jane'
const proxy = new Proxy(jane, {});
proxy.name // undefined
```

Proxy 对象可以拦截目标对象的任意属性，这使得它很合适用来写 Web 服务的客户端。Proxy 也可以用来实现数据库的 ORM 层。

```js
//Proxy 可以拦截接口对象的任意属性，所以不用为每一种数据写一个适配方法，只要写一个 Proxy 拦截就可以了。
function createWebService(baseUrl) {
  return new Proxy({}, {
    get(target, propKey, receiver) {
      return () => httpGet(baseUrl + '/' + propKey);
    }
  });
}

const service = createWebService('http://example.com/data');
service.employees().then(json => {
  const employees = JSON.parse(json);
  // ···
});
```



### 2.Reflect

`Reflect`对象的设计目的：

*  将`Object`对象的一些明显属于语言内部的方法（比如`Object.defineProperty`），放到`Reflect`对象上。
* 修改某些`Object`方法的返回结果，让其变得更合理。
* 让`Object`操作都变成函数行为。
* `Reflect`对象的方法与`Proxy`对象的方法一一对应，只要是`Proxy`对象的方法，就能在`Reflect`对象上找到对应的方法。

**Reflect提供的静态方法：**

| 静态方法 | 说明 |
| -------- | ---- |
| Reflect.get(target, name, receiver) | `Reflect.get`方法查找并返回`target`对象的`name`属性，如果没有该属性，则返回`undefined`。 |
| Reflect.set(target, name, value, receiver) | `Reflect.set`方法设置`target`对象的`name`属性等于`value`。 |
| Reflect.has(obj, name) | `Reflect.has`方法对应`name in obj`里面的`in`运算符。 |
| Reflect.deleteProperty(obj, name) | `Reflect.deleteProperty`方法等同于`delete obj[name]`，用于删除对象的属性。 |
| Reflect.construct(target, args) | `Reflect.construct`方法等同于`new target(...args)`，这提供了一种不使用`new`，来调用构造函数的方法。 |
| Reflect.getPrototypeOf(obj) | `Reflect.getPrototypeOf`方法用于读取对象的`__proto__`属性，对应`Object.getPrototypeOf(obj)`。 |
| Reflect.setPrototypeOf(obj, newProto) | `Reflect.setPrototypeOf`方法用于设置目标对象的原型（prototype），对应`Object.setPrototypeOf(obj, newProto)`方法。它返回一个布尔值，表示是否设置成功。 |
| Reflect.apply(func, thisArg, args) | `Reflect.apply`方法等同于`Function.prototype.apply.call(func, thisArg, args)`，用于绑定`this`对象后执行给定函数。 |
| Reflect.defineProperty(target, propertyKey, attributes) | `Reflect.defineProperty`方法基本等同于`Object.defineProperty`，用来为对象定义属性。未来，后者会被逐渐废除，请从现在开始就使用`Reflect.defineProperty`代替它。 |
| Reflect.getOwnPropertyDescriptor(target, propertyKey) | `Reflect.getOwnPropertyDescriptor`基本等同于`Object.getOwnPropertyDescriptor`，用于得到指定属性的描述对象，将来会替代掉后者。 |
| Reflect.isExtensible (target) | `Reflect.isExtensible`方法对应`Object.isExtensible`，返回一个布尔值，表示当前对象是否可扩展。 |
| Reflect.preventExtensions(target) | `Reflect.preventExtensions`对应`Object.preventExtensions`方法，用于让一个对象变为不可扩展。它返回一个布尔值，表示是否操作成功。 |
| Reflect.ownKeys (target) | `Reflect.ownKeys`方法用于返回对象的所有属性，基本等同于`Object.getOwnPropertyNames`与`Object.getOwnPropertySymbols`之和。 |



### 3.使用`Proxy`实现观察者模式

```js
//先定义了一个Set集合，所有观察者函数都放进这个集合
const queuedObservers = new Set();
const observe = fn => queuedObservers.add(fn);
//observable函数返回原始对象的代理，拦截赋值操作。拦截函数set之中，会自动执行所有观察者。
const observable = obj => new Proxy(obj, { set });
function set(target, key, value, receiver) {
    const result = Reflect.set(target, key, value, receiver);
    queuedObservers.forEach(observer => observer());
    return result;
}


const person = observable({
    name: '张三',
    age: 20
});
function print() {
    console.log(`${person.name}, ${person.age}`)
}
observe(print);
person.name = '李四';
  // 输出
  // 李四, 20
```



## 十二、Promise异步编程

Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。它由社区最早提出和实现，ES6 将其写进了语言标准，统一了用法，原生提供了`Promise`对象。

`Promise`对象代表一个异步操作，有三种状态：`pending`（进行中）、`fulfilled`（已成功）和`rejected`（已失败）。`Promise`对象的状态改变，只有两种可能：从`pending`变为`fulfilled`和从`pending`变为`rejected`。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果，这时就称为 resolved（已定型）。

ES6 规定，`Promise`对象是一个构造函数，用来生成`Promise`实例。`Promise`构造函数接受一个函数作为参数，该函数的两个参数分别是`resolve`和`reject`。它们是两个函数，由 JavaScript 引擎提供，不用自己部署。`Promise`实例生成以后，可以用`then`方法分别指定`resolved`状态和`rejected`状态的回调函数。

```js
const promise = new Promise(function(resolve, reject) {
  // ... some code 执行异步业务操作
  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});

promise.then(function(value) {
  // success 成功回调
}, function(error) {
  // failure 失败回调
});
```



```js
const getJSON = function(url) {
  const promise = new Promise(function(resolve, reject){
    const handler = function() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
    const client = new XMLHttpRequest();
    client.open("GET", url);
    client.onreadystatechange = handler;
    client.responseType = "json";
    client.setRequestHeader("Accept", "application/json");
    client.send();
  });
  return promise;
};
getJSON("/posts.json").then(function(json) {
  console.log('Contents: ' + json);
}, function(error) {
  console.error('出错了', error);
});
```



* `Promise.resolve()`

  有时需要将现有对象转为 Promise 对象，`Promise.resolve()`方法就起到这个作用。

  ```js
  const jsPromise = Promise.resolve($.ajax('/whatever.json'));
  ```

  ```js
  //参数是一个thenable对象
  let thenable = {
    then: function(resolve, reject) {
      resolve(42);
    }
  };
  let p1 = Promise.resolve(thenable);
  p1.then(function(value) {
    console.log(value);  // 42
  });
  
  //如果参数是一个原始值，或者是一个不具有then方法的对象，则Promise.resolve方法返回一个新的 Promise 对象，状态为resolved。
  const p = Promise.resolve('Hello');
  p.then(function (s){
    console.log(s)
  });
  // Hello
  
  //Promise.resolve()方法允许调用时不带参数，直接返回一个resolved状态的 Promise 对象。
  const p = Promise.resolve();
  p.then(function () {
    // ...
  });
  ```

  

* `Promise.reject()`

  `Promise.reject(reason)`方法也会返回一个新的 Promise 实例，该实例的状态为`rejected`。

  ```js
  const p = Promise.reject('出错了');
  p.then(null, function (s) {
    console.log(s)
  });
  // 出错了
  ```

  

* `Promise.try()`

  有没有一种方法，让同步函数同步执行，异步函数异步执行，并且让它们具有统一的 API 呢？`Promise.try`为所有操作提供了统一的处理机制，如果想用`then`方法管理流程，最好都用`Promise.try`包装一下。这样有许多好处，其中一点就是可以更好地管理异常。

  ```js
  const f = () => console.log('now');
  Promise.try(f);
  console.log('next');
  // now
  // next
  
  Promise.try(() => database.users.get({id: userId}))
    .then(...)
    .catch(...)//处理异常
  ```

  

* `Promise.prototype.then()`

  Promise 实例具有`then`方法，也就是说，`then`方法是定义在原型对象`Promise.prototype`上的。它的作用是为 Promise 实例添加状态改变时的回调函数。`then`方法返回的是一个新的`Promise`实例（注意，不是原来那个`Promise`实例）。因此可以采用链式写法，即`then`方法后面再调用另一个`then`方法。

  ```js
  getJSON("/post/1.json").then(
    post => getJSON(post.commentURL)
  ).then(
    comments => console.log("resolved: ", comments),
    err => console.log("rejected: ", err)
  );
  ```

  

* `Promise.prototype.catch()`

  `Promise.prototype.catch()`方法是`.then(null, rejection)`或`.then(undefined, rejection)`的别名，用于指定发生错误时的回调函数。

  ```js
  getJSON('/posts.json').then(function(posts) {
    // ...
  }).catch(function(error) {
    // 处理 getJSON 和 前一个回调函数运行时发生的错误
    console.log('发生错误！', error);
  });
  ```

  

* `Promise.prototype.finally()`

  `finally()`方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。该方法是 ES2018 引入标准的。

  ```js
  promise
  .then(result => {···})
  .catch(error => {···})
  .finally(() => {···});
  ```

  

* `Promise.all()`

  `Promise.all()`方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。`Promise.all()`方法的参数可以不是数组，但必须具有 Iterator 接口，且返回的每个成员都是 Promise 实例，如果不是，就会先调用`Promise.resolve`方法，将参数转为 Promise 实例，再进一步处理。

  ```js
  const p = Promise.all([p1, p2, p3]);
  ```

  `p`的状态由`p1`、`p2`、`p3`决定，分成两种情况：

  1. 只有`p1`、`p2`、`p3`的状态都变成`fulfilled`，`p`的状态才会变成`fulfilled`，此时`p1`、`p2`、`p3`的返回值组成一个数组，传递给`p`的回调函数。
  2. 只要`p1`、`p2`、`p3`之中有一个被`rejected`，`p`的状态就变成`rejected`，此时第一个被`reject`的实例的返回值，会传递给`p`的回调函数。

  注意，如果作为参数的 Promise 实例，自己定义了`catch`方法，那么它一旦被`rejected`，并不会触发`Promise.all()`的`catch`方法。

  ```js
  const p1 = new Promise((resolve, reject) => {
    resolve('hello');
  })
  .then(result => result)
  .catch(e => e);
  const p2 = new Promise((resolve, reject) => {
    throw new Error('报错了');
  })
  .then(result => result)
  .catch(e => e);
  Promise.all([p1, p2])
  .then(result => console.log(result))
  .catch(e => console.log(e));
  // ["hello", Error: 报错了]
  ```

  

* `Promise.race()`

  `Promise.race()`方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。`Promise.race()`方法的参数与`Promise.all()`方法一样，如果不是 Promise 实例，就会先调用下面讲到的`Promise.resolve()`方法，将参数转为 Promise 实例，再进一步处理。

  ```js
  const p = Promise.race([p1, p2, p3]);
  ```

  只要`p1`、`p2`、`p3`之中有一个实例率先改变状态，`p`的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给`p`的回调函数。

  ```js
  //如果指定时间5s内没有获得结果，就将 Promise 的状态变为reject，否则变为resolve。
  const p = Promise.race([
    fetch('/resource-that-may-take-a-while'),
    new Promise(function (resolve, reject) {
      setTimeout(() => reject(new Error('request timeout')), 5000)
    })
  ]);
  p
  .then(console.log)
  .catch(console.error);
  ```

  

* `Promise.allSettled()`

  `Promise.allSettled()`方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例。只有等到所有这些参数实例都返回结果，不管是`fulfilled`还是`rejected`，包装实例才会结束。该方法由ES2020 引入。该方法返回的新的 Promise 实例，一旦结束，状态总是`fulfilled`，不会变成`rejected`。状态变成`fulfilled`后，Promise 的监听函数接收到的参数是一个数组，每个成员对应一个传入`Promise.allSettled()`的 Promise 实例。

  ```js
  const resolved = Promise.resolve(42);
  const rejected = Promise.reject(-1);
  const allSettledPromise = Promise.allSettled([resolved, rejected]);
  allSettledPromise.then(function (results) {
    console.log(results);
  });
  // [
  //    { status: 'fulfilled', value: 42 },
  //    { status: 'rejected', reason: -1 }
  // ]
  ```

  有时候，我们不关心异步操作的结果，只关心这些操作有没有结束。这时，`Promise.allSettled()`方法就很有用。如果没有这个方法，想要确保所有操作都结束，就很麻烦。`Promise.all()`方法无法做到这一点。

  

* `Promise.any()`

  `Promise.any()`方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例。只要参数实例有一个变成`fulfilled`状态，包装实例就会变成`fulfilled`状态；如果所有参数实例都变成`rejected`状态，包装实例就会变成`rejected`状态。该方法目前是一个第三阶段的提案 。`Promise.any()`跟`Promise.race()`方法很像，只有一点不同，就是不会因为某个 Promise 变成`rejected`状态而结束。

  ```js
  Promise.any(promises).then(
    (first) => {
      // Any of the promises was fulfilled.
    },
    (error) => {
      // All of the promises were rejected.
    }
  );
  ```

  

## 十三、Iterator接口

JavaScript 原有的表示“集合”的数据结构，主要是数组（`Array`）和对象（`Object`），ES6 又添加了`Map`和`Set`。这样就需要一种统一的接口机制，来处理所有不同的数据结构。遍历器（Iterator）就是这样一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）。

模拟Iterator：

```js
function makeIterator(array) {
  var nextIndex = 0;
  return {
    next: function() {
      return nextIndex < array.length ?
        {value: array[nextIndex++]} :
        {done: true};
    }
  };
}
```

ES6 规定，默认的 Iterator 接口部署在数据结构的`Symbol.iterator`属性，或者说，一个数据结构只要具有`Symbol.iterator`属性，就可以认为是“可遍历的”（iterable）。`Symbol.iterator`属性本身是一个函数，就是当前数据结构默认的遍历器生成函数。执行这个函数，就会返回一个遍历器。至于属性名`Symbol.iterator`，它是一个表达式，返回`Symbol`对象的`iterator`属性，这是一个预定义好的、类型为 Symbol 的特殊值，所以要放在方括号内。

```js
const obj = {
  [Symbol.iterator] : function () {
    return {
      next: function () {
        return {
          value: 1,
          done: true
        };
      }
    };
  }
};
```

ES6 的有些数据结构原生具备 Iterator 接口（比如数组），即不用任何处理，就可以被`for...of`循环遍历。原生具备 Iterator 接口的数据结构如下:

- Array

  ```js
  let arr = ['a', 'b', 'c'];
  let iter = arr[Symbol.iterator]();
  iter.next() // { value: 'a', done: false }
  iter.next() // { value: 'b', done: false }
  iter.next() // { value: 'c', done: false }
  iter.next() // { value: undefined, done: true }
  ```

  

- Map

- Set

- String

  ```js
  var someString = "hi";
  typeof someString[Symbol.iterator]
  // "function"
  var iterator = someString[Symbol.iterator]();
  iterator.next()  // { value: "h", done: false }
  iterator.next()  // { value: "i", done: false }
  iterator.next()  // { value: undefined, done: true }
  ```

  

- TypedArray

- 函数的 arguments 对象

- NodeList 对象



**`for ... in` 和 `for ... of` 的区别：`for...in`循环读取键名，`for...of`循环读取键值。**



## 十四、Generator

### 1.语法及应用场景介绍

Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。

* 语法上，首先可以把它理解成，Generator 函数是一个状态机，封装了多个内部状态。

  执行 Generator 函数会返回一个遍历器对象，也就是说，Generator 函数除了状态机，还是一个遍历器对象生成函数。返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。

* 形式上，Generator 函数是一个普通函数，但是有两个特征。

  一是，`function`关键字与函数名之间有一个星号`*`；

  二是，函数体内部使用`yield`表达式，定义不同的内部状态（`yield`在英语里的意思就是“产出”）。

```js
function* helloWorldGenerator() {
    yield 'hello';
    yield 'world';
  }
  let hw = helloWorldGenerator();
  for( let output of hw){
    console.log(output)
  }
//hello
//world
```

由于 Generator 函数返回的遍历器对象，只有调用`next`方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。`yield`表达式就是暂停标志。遍历器对象的`next`方法的运行逻辑如下:

* 遇到`yield`表达式，就暂停执行后面的操作，并将紧跟在`yield`后面的那个表达式的值，作为返回的对象的`value`属性值。
* 下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式。
* 如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的`value`属性值。
* 如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

`yield`表达式需要注意的地方：

* `yield`表达式只能用在 Generator 函数里面，用在其他地方都会报错。

* `yield`表达式如果用在另一个表达式之中，必须放在圆括号里面。

  ```js
  function* demo() {
    console.log('Hello' + yield); // SyntaxError
    console.log('Hello' + yield 123); // SyntaxError
    console.log('Hello' + (yield)); // OK
    console.log('Hello' + (yield 123)); // OK
  }
  ```

  

* `yield`表达式用作函数参数或放在赋值表达式的右边，可以不加括号。

  ```js
  function* demo() {
    foo(yield 'a', yield 'b'); // OK
    let input = yield; // OK
  }
  ```

  

应用场景：

* 异步操作的同步化

  ```js
  function* loadUI() {
    showLoadingScreen();
    yield loadUIDataAsynchronously();
    hideLoadingScreen();
  }
  var loader = loadUI();
  // 加载UI
  loader.next()
  // 卸载UI
  loader.next()
  ```

  

* 控制流管理

  ```js
  function* longRunningTask(value1) {
    try {
      var value2 = yield step1(value1);
      var value3 = yield step2(value2);
      var value4 = yield step3(value3);
      var value5 = yield step4(value4);
      // Do something with value4
    } catch (e) {
      // Handle any error from step1 through step4
    }
  }
  ```

  ```js
  let jobs = [job1, job2, job3];
  function* iterateJobs(jobs){
    for (var i=0; i< jobs.length; i++){
      var job = jobs[i];
      yield* iterateSteps(job.steps);
    }
  }
  for (var step of iterateJobs(jobs)){
    console.log(step.id);
  }
  ```

  

* 部署 Iterator 接口

  ```js
  function* iterEntries(obj) {
    let keys = Object.keys(obj);
    for (let i=0; i < keys.length; i++) {
      let key = keys[i];
      yield [key, obj[key]];
    }
  }
  let myObj = { foo: 3, bar: 7 };
  for (let [key, value] of iterEntries(myObj)) {
    console.log(key, value);
  }
  // foo 3
  // bar 7
  ```

  

* 作为数组数据结构

  ```js
  function* doStuff() {
    yield fs.readFile.bind(null, 'hello.txt');
    yield fs.readFile.bind(null, 'world.txt');
    yield fs.readFile.bind(null, 'and-such.txt');
  }
  for (task of doStuff()) {
    // task是一个函数，可以像回调函数那样使用它
  }
  ```



### 2.next的参数

**`yield`表达式本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会被当作上一个`yield`表达式的返回值。**

```js
function* f() {
  for(var i = 0; true; i++) {
    var reset = yield i;
    if(reset) { i = -1; }
  }
}
var g = f();
g.next() // { value: 0, done: false }
g.next() // { value: 1, done: false }
g.next(true) // { value: 0, done: false }
```

Generator 函数从暂停状态到恢复运行，它的上下文状态（context）是不变的。通过`next`方法的参数，就有办法在 Generator 函数开始运行之后，继续向函数体内部注入值。也就是说，可以在 Generator 函数运行的不同阶段，从外部向内部注入不同的值，从而调整函数行为。

```js
function* foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);
  return (x + y + z);
}
var a = foo(5);
a.next() // Object{value:6, done:false}
a.next() // Object{value:NaN, done:false}
a.next() // Object{value:NaN, done:true}
var b = foo(5);
b.next() // { value:6, done:false }
b.next(12) // { value:8, done:false }
b.next(13) // { value:42, done:true }
```



### 3.Generator.prototype.throw()

Generator 函数返回的遍历器对象，都有一个`throw`方法，可以在函数体外抛出错误，然后在 Generator 函数体内捕获。

* `throw`方法抛出的错误要被内部捕获，前提是必须至少执行过一次`next`方法。
* `throw`方法可以接受一个参数，该参数会被`catch`语句接收，建议抛出`Error`对象的实例。
* 如果 Generator 函数内部没有部署`try...catch`代码块，那么`throw`方法抛出的错误，将被外部`try...catch`代码块捕获。
* `throw`方法被捕获以后，会附带执行下一条`yield`表达式。也就是说，会附带执行一次`next`方法。
* 一旦 Generator 执行过程中抛出错误，且没有被内部捕获，就不会再执行下去了。如果此后还调用`next`方法，将返回一个`value`属性等于`undefined`、`done`属性等于`true`的对象，即 JavaScript 引擎认为这个 Generator 已经运行结束了。

```js
var gen = function* gen(){
  try {
    yield console.log('a');
  } catch (e) {
    // ...
  }
  yield console.log('b');
  yield console.log('c');
}
var g = gen();
g.next() // a
g.throw() // b
g.next() // c
```

### 4.Generator.prototype.return()

Generator 函数返回的遍历器对象，还有一个`return`方法，可以返回给定的值，并且终结遍历 Generator 函数。

* 如果`return`方法调用时，不提供参数，则返回值的`value`属性为`undefined`。
* 如果 Generator 函数内部有`try...finally`代码块，且正在执行`try`代码块，那么`return`方法会导致立刻进入`finally`代码块，执行完以后，整个函数才会结束。

```js
function* gen() {
  yield 1;
  yield 2;
  yield 3;
}
var g = gen();
g.next()        // { value: 1, done: false }
g.return('foo') // { value: "foo", done: true }
g.next()        // { value: undefined, done: true }
```



```js
function* numbers () {
  yield 1;
  try {
    yield 2;
    yield 3;
  } finally {
    yield 4;
    yield 5;
  }
  yield 6;
}
var g = numbers();
g.next() // { value: 1, done: false }
g.next() // { value: 2, done: false }
g.return(7) // { value: 4, done: false }
g.next() // { value: 5, done: false }
g.next() // { value: 7, done: true }
```



### 5.`yield*`表达式

ES6 提供了`yield*`表达式，用来在一个 Generator 函数里面执行另一个 Generator 函数。

```js
let delegatedIterator = (function* () {
  yield 'Hello!';
  yield 'Bye!';
}());
let delegatingIterator = (function* () {
  yield 'Greetings!';
  yield* delegatedIterator;
  yield 'Ok, bye.';
}());
for(let value of delegatingIterator) {
  console.log(value);
}
// "Greetings!
// "Hello!"
// "Bye!"
// "Ok, bye."
```

实际上，任何数据结构只要有 Iterator 接口，就可以被`yield*`遍历。

### 6.作为对象属性的Generator

如果一个对象的属性是 Generator 函数，可以简写成下面的形式。

```js
let obj = {
  * myGeneratorMethod() {
    ···
  }
};

//等同于
let obj = {
  myGeneratorMethod: function* () {
    // ···
  }
};
```

### 7.Generator中的this

Generator 函数总是返回一个遍历器，ES6 规定这个遍历器是 Generator 函数的实例，也继承了 Generator 函数的`prototype`对象上的方法。Generator 函数也不能跟`new`命令一起用，会报错。使用`call`方法绑定 Generator 函数内部的`this`：

```js
function* F() {
  this.a = 1;
  yield this.b = 2;
  yield this.c = 3;
}
//首先，生成一个空对象，使用call方法绑定 Generator 函数内部的this。这样，构造函数调用以后，这个空对象就是 Generator 函数的实例对象了。
var obj = {};
var f = F.call(obj);
f.next();  // Object {value: 2, done: false}
f.next();  // Object {value: 3, done: false}
f.next();  // Object {value: undefined, done: true}
obj.a // 1
obj.b // 2
obj.c // 3
```

再将`F`改成构造函数，就可以对它执行`new`命令了。

```js
function* gen() {
  this.a = 1;
  yield this.b = 2;
  yield this.c = 3;
}
function F() {
  return gen.call(gen.prototype);
}
var f = new F();
f.next();  // Object {value: 2, done: false}
f.next();  // Object {value: 3, done: false}
f.next();  // Object {value: undefined, done: true}
f.a // 1
f.b // 2
f.c // 3
```



### 8.异步编程

ES6 诞生以前，异步编程的方法，大概有下面四种。

- 回调函数
- 事件监听
- 发布/订阅
- Promise 对象

Generator 函数将 JavaScript 异步编程带入了一个全新的阶段。

Generator 函数是协程在 ES6 的实现，最大特点就是可以交出函数的执行权（即暂停执行）。整个 Generator 函数就是一个封装的异步任务，或者说是异步任务的容器。异步操作需要暂停的地方，都用`yield`语句注明。

```js
var fetch = require('node-fetch');
function* gen(){
  var url = 'https://api.github.com/users/github';
  var result = yield fetch(url);
  console.log(result.bio);
}
var g = gen();
var result = g.next();
result.value.then(function(data){
  return data.json();
}).then(function(data){
  g.next(data);
});
```

上面代码中，首先执行 Generator 函数，获取遍历器对象，然后使用`next`方法（第二行），执行异步任务的第一阶段。由于`Fetch`模块返回的是一个 Promise 对象，因此要用`then`方法调用下一个`next`方法。

### 9.Thunk函数与Generator自动执行

Thunk函数是"传名调用"的一种实现策略。将表达式参数放到一个临时函数之中，再将这个临时函数传入函数体。这个临时函数就叫做 Thunk 函数。

```js
// ES6版本
const Thunk = function(fn) {
  return function (...args) {
    return function (callback) {
      return fn.call(this, ...args, callback);
    }
  };
};

function f(a, cb) {
  cb(a);
}
const ft = Thunk(f);
ft(1)(console.log) // 1
```

Thunk 函数真正的威力，在于可以自动执行 Generator 函数。下面就是一个基于 Thunk 函数的 Generator 执行器。

```js
//自动执行器
let run = (fn) => {
    let gen = fn();
    let next = () => {
        let result = gen.next();
        if (result.done) return;
        result.value();
        next();
    }
    next();
}
//thunk函数转换器
function toThunk(fn) {
    return (...args) => {
        return (callback) => {
            fn.call(this, ...args, callback);
        };
    };
}
//回调函数
let callback = (data) => {
    console.log(data)
}
//Generator
let g = function* () {
    yield toThunk(callback)(1);
    yield toThunk(callback)(2);
    yield toThunk(callback)(3);
}

run(g);
```

### 10.`co`模块

[co 模块](https://github.com/tj/co)是著名程序员 TJ Holowaychuk 于 2013 年 6 月发布的一个小工具，用于 Generator 函数的自动执行。

co 模块可以让你不用编写 Generator 函数的执行器。`co`函数返回一个`Promise`对象，因此可以用`then`方法添加回调函数。

```js
var gen = function* () {
  var f1 = yield readFile('/etc/fstab');
  var f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
var co = require('co');
co(gen).then(function (){
  console.log('Generator 函数执行完成');
});

```

co 支持并发的异步操作，即允许某些操作同时进行，等到它们全部完成，才进行下一步。这时，要把并发的操作都放在数组或对象里面，跟在`yield`语句后面。

```js
const co = require('co')
let onerror = (error)=>{
  console.log(error)
}
// 数组的写法
co(function* () {
  var res = yield [
    Promise.resolve(1),
    Promise.resolve(2)
  ];
  console.log(res);
}).catch(onerror);
// 对象的写法
co(function* () {
  var res = yield {
    1: Promise.resolve(1),
    2: Promise.resolve(2),
  };
  console.log(res);
}).catch(onerror);
```

`co`模块约定，`yield`命令后面只能是 Thunk 函数或 Promise 对象。

## 十五、async函数

ES2017 标准引入了 async 函数，使得异步操作变得更加方便。一句话，它就是 Generator 函数的语法糖。一比较就会发现，`async`函数就是将 Generator 函数的星号（`*`）替换成`async`，将`yield`替换成`await`，仅此而已。async 函数的实现原理，就是将 Generator 函数和自动执行器，包装在一个函数里。

```js
const gen = function* () {
  const f1 = yield readFile('/etc/fstab');
  const f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};

const asyncReadFile = async function () {
  const f1 = await readFile('/etc/fstab');
  const f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

`async`函数对 Generator 函数的改进:

* 内置执行器

  Generator 函数的执行必须靠执行器，所以才有了`co`模块，而`async`函数自带执行器。也就是说，`async`函数的执行，与普通函数一模一样，只要一行。

  ```js
  asyncReadFile();
  ```

  

* 更好的语义

  `async`和`await`，比起星号和`yield`，语义更清楚了。`async`表示函数里有异步操作，`await`表示紧跟在后面的表达式需要等待结果。

* 更广的适用性

  `co`模块约定，`yield`命令后面只能是 Thunk 函数或 Promise 对象，而`async`函数的`await`命令后面，可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时会自动转成立即 resolved 的 Promise 对象）。

* 返回值是 Promise

  `async`函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便多了。你可以用`then`方法指定下一步的操作。

### 1.async 函数的使用形式

```js
// 函数声明
async function foo() {}
// 函数表达式
const foo = async function () {};
// 对象的方法
let obj = { async foo() {} };
obj.foo().then(...)
// Class 的方法
class Storage {
  constructor() {
    this.cachePromise = caches.open('avatars');
  }
  async getAvatar(name) {
    const cache = await this.cachePromise;
    return cache.match(`/avatars/${name}.jpg`);
  }
}
const storage = new Storage();
storage.getAvatar('jake').then(…);
// 箭头函数
const foo = async () => {};
```

### 2.async函数的返回值

`async`函数返回一个 Promise 对象。`async`函数内部`return`语句返回的值，会成为`then`方法回调函数的参数。`async`函数内部抛出错误，会导致返回的 Promise 对象变为`reject`状态。抛出的错误对象会被`catch`方法回调函数接收到。

```js
async function f() {
  return 'hello world';
}
f().then(v => console.log(v))
// "hello world"

async function f() {
  throw new Error('出错了');
}
f().then(
  v => console.log(v),
  e => console.log(e)
)
// Error: 出错了
```

### 3.await

`async`函数返回的 Promise 对象，必须等到内部所有`await`命令后面的 Promise 对象执行完，才会发生状态改变，除非遇到`return`语句或者抛出错误。也就是说，只有`async`函数内部的异步操作执行完，才会执行`then`方法指定的回调函数。正常情况下，`await`命令后面是一个 Promise 对象，返回该对象的结果。如果不是 Promise 对象，就直接返回对应的值。另一种情况是，`await`命令后面是一个`thenable`对象（即定义了`then`方法的对象），那么`await`会将其等同于 Promise 对象。

```js
async function f() {
  // 等同于
  // return 123;
  return await 123;
}
f().then(v => console.log(v))
// 123


class Sleep {
  constructor(timeout) {
    this.timeout = timeout;
  }
  then(resolve, reject) {
    const startTime = Date.now();
    setTimeout(
      () => resolve(Date.now() - startTime),
      this.timeout
    );
  }
}
(async () => {
  const sleepTime = await new Sleep(1000);
  console.log(sleepTime);
})();
// 1000
```

`await`命令后面的 Promise 对象如果变为`reject`状态，则`reject`的参数会被`catch`方法的回调函数接收到。任何一个`await`语句后面的 Promise 对象变为`reject`状态，那么整个`async`函数都会中断执行。

```js
async function f() {
  await Promise.reject('出错了');
}
f()
.then(v => console.log(v))
.catch(e => console.log(e))
// 出错了

async function f() {
  await Promise.reject('出错了');
  await Promise.resolve('hello world'); // 不会执行
}
```

有时，我们希望即使前一个异步操作失败，也不要中断后面的异步操作。这时可以将第一个`await`放在`try...catch`结构里面，这样不管这个异步操作是否成功，第二个`await`都会执行。另一种方法是`await`后面的 Promise 对象再跟一个`catch`方法，处理前面可能出现的错误。

```js
async function f() {
  try {
    await Promise.reject('出错了');
  } catch(e) {
  }
  return await Promise.resolve('hello world');
}
f()
.then(v => console.log(v))
// hello world

async function f() {
  await Promise.reject('出错了')
    .catch(e => console.log(e));
  return await Promise.resolve('hello world');
}
f()
.then(v => console.log(v))
// 出错了
// hello world
```

**`await`命令只能用在`async`函数之中，如果用在普通函数，就会报错。**



## 十六、Class（类）

### 1.类的定义

ES6 提供了更接近传统语言的写法，引入了 Class（类）这个概念，作为对象的模板。通过`class`关键字，可以定义类。

```js
//ES5
function Point(x, y) {
  this.x = x;
  this.y = y;
}
Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};
var p = new Point(1, 2);

//ES6
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
```

构造函数的`prototype`属性，在 ES6 的“类”上面继续存在。事实上，类的所有方法都定义在类的`prototype`属性上面。

```js
class Point {
  constructor() {
    // ...
  }
  toString() {
    // ...
  }
  toValue() {
    // ...
  }
}
// 等同于
Point.prototype = {
  constructor() {},
  toString() {},
  toValue() {},
};
```

### 2.构造方法

`constructor`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。定义了其他带参数的`constructor`方法，默认空的`constructor`方法也还是存在的。

```js
class Point {
}
// 等同于
class Point {
  constructor() {}
}
```

```js
class Point {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }

    toString() {
        return `{ x: ${this.x}, y: ${this.y}}`;
    }

}

let point = new Point();
console.log(point.toString());//{ x: undefined, y: undefined}

let point1 = new Point(1,2);
console.log(point1.toString());//{ x: 1, y: 2}
```

ES6 为`new`命令引入了一个`new.target`属性，该属性一般用在构造函数之中，返回`new`命令作用于的那个构造函数。如果构造函数不是通过`new`命令或`Reflect.construct()`调用的，`new.target`会返回`undefined`，因此这个属性可以用来确定构造函数是怎么调用的。需要注意的是，子类继承父类时，`new.target`会返回子类。

```js
class Rectangle {
  constructor(length, width) {
    console.log(new.target === Rectangle);
    this.length = length;
    this.width = width;
  }
}
var obj = new Rectangle(3, 4); // 输出 true
```

利用这个特点，可以写出不能独立使用、必须继承后才能使用的类。

```js
class Shape {
  constructor() {
    if (new.target === Shape) {
      throw new Error('本类不能实例化');
    }
  }
}
class Rectangle extends Shape {
  constructor(length, width) {
    super();
    // ...
  }
}
var x = new Shape();  // 报错
var y = new Rectangle(3, 4);  // 正确
```



### 3.`this`和类原型

与 ES5 一样，实例的属性除非显式定义在其本身（即定义在`this`对象上），否则都是定义在原型上（即定义在`class`上）。类的所有实例共享一个原型对象。

```js
//定义类
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
var point = new Point(2, 3);
point.toString() // (2, 3)
point.hasOwnProperty('x') // true
point.hasOwnProperty('y') // true
point.hasOwnProperty('toString') // false
point.__proto__.hasOwnProperty('toString') // true


var p1 = new Point(2,3);
var p2 = new Point(3,2);
p1.__proto__ === p2.__proto__//类的所有实例共享一个原型对象
//true
```

因为类的所有实例都共享一个原型对象，所以`__proto__`属性是相等的。这也意味着，可以通过实例的`__proto__`属性为“类”添加方法。

```js
var p1 = new Point(2,3);
var p2 = new Point(3,2);
p1.__proto__.printName = function () { return 'Oops' };
p1.printName() // "Oops"
p2.printName() // "Oops"
var p3 = new Point(4,2);
p3.printName() // "Oops"
```

**注意：类的方法内部如果含有`this`，它默认指向类的实例。**

### 4.`getter`和`setter`

与 ES5 一样，在“类”的内部可以使用`get`和`set`关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为。

```js
class CustomHTMLElement {
  constructor(element) {
    this.element = element;
  }
  get html() {
    return this.element.innerHTML;
  }
  set html(value) {
    this.element.innerHTML = value;
  }
}
var descriptor = Object.getOwnPropertyDescriptor(
  CustomHTMLElement.prototype, "html"
);
"get" in descriptor  // true
"set" in descriptor  // true
```



### 5.表达式

类的属性名，可以采用表达式。

```js
let methodName = 'getArea';
class Square {  
  constructor(length) {    
    // ...  
  }  
  [methodName]() {    
    // ...  
  }
}
```

类也可以使用表达式的形式定义：

```js
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};
//如果类的内部没用到的话，可以省略Me
const MyClass = class { /* ... */ };
```

采用class表达式写匿名类：

```js
let person = new class {
  constructor(name) {
    this.name = name;
  }
  sayName() {
    console.log(this.name);
  }
}('张三');
person.sayName(); // "张三"
```



### 6.严格模式

类和模块的内部，默认就是严格模式，所以不需要使用`use strict`指定运行模式。只要你的代码写在类或模块之中，就只有严格模式可用。考虑到未来所有的代码，其实都是运行在模块之中，所以 ES6 实际上把整个语言升级到了严格模式。



### 7.方法

* 实例方法

  ```js
  class Foo {
    method(){
      //...
    }
  }
  let foo = new Foo();
  foo.method();
  ```

  

* 静态方法

  如果在一个方法前，加上`static`关键字，就表示该方法直接通过类来调用，这就称为“静态方法”。

  ```js
  class Foo {
    static classMethod() {
      return 'hello';
    }
  }
  Foo.classMethod() // 'hello'
  var foo = new Foo();
  foo.classMethod()
  // TypeError: foo.classMethod is not a function
  ```

  注意，如果静态方法包含`this`关键字，这个`this`指的是类，而不是实例。

  ```js
  class Foo {
    static bar() {
      this.baz();
    }
    static baz() {
      console.log('hello');
    }
    baz() {
      console.log('world');
    }
  }
  Foo.bar() // hello
  
  ```

  

* 私有方法

  在方法名之前，使用`#`表示。

  ```js
  class Foo {
    #a;
    #b;
    constructor(a, b) {
      this.#a = a;
      this.#b = b;
    }
    #sum() {
      return #a + #b;
    }
    printSum() {
      console.log(this.#sum());
    }
  }
  ```

  私有方法前面，也可以加上`static`关键字，表示这是一个静态的私有方法。



### 8.属性

* 实例属性

  实例属性除了定义在`constructor()`方法里面的`this`上面，也可以定义在类的最顶层。

  ```js
  class foo {
    bar = 'hello';
    baz = 'world';
    constructor() {
      // ...
    }
  }
  ```

  

* 类静态属性

  静态属性指的是 Class 本身的属性，即`Class.propName`，而不是定义在实例对象（`this`）上的属性。写法是在实例属性的前面，加上`static`关键字。

  ```js
  // 老写法
  class Foo {
    // ...
  }
  Foo.prop = 1;
  // 新写法
  class Foo {
    static prop = 1;
  }
  ```

* 私有属性

  在属性名之前，使用`#`表示。

  ```js
  class IncreasingCounter {
    #count = 0;
    get value() {
      console.log('Getting the current value!');
      return this.#count;
    }
    increment() {
      this.#count++;
    }
  }
  ```

  私有属性前面也可以加上`static`关键字，表示这是一个静态的私有属性。

### 9.继承

Class 可以通过`extends`关键字实现继承，这比 ES5 的通过修改原型链实现继承，要清晰和方便很多。

```js
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
}
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y);
    this.color = color;
  }
}
let cp = new ColorPoint(25, 8, 'green');
cp instanceof ColorPoint // true
cp instanceof Point // true
```

* 子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错。

  这是因为子类自己的`this`对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用`super`方法，子类就得不到`this`对象。

* 在子类的构造函数中，只有调用`super`之后，才可以使用`this`关键字，否则会报错。

  这是因为子类实例的构建，基于父类实例，只有`super`方法才能调用父类实例。

  

`Object.getPrototypeOf`方法可以用来从子类上获取父类。因此，可以使用这个方法判断，一个类是否继承了另一个类。

```js
Object.getPrototypeOf(ColorPoint) === Point
// true
```

`super`这个关键字，既可以当作父类的构造函数使用，也可以当作对象使用。`super`作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。ES6 规定，在子类普通方法中通过`super`调用父类的方法时，方法内部的`this`指向当前的子类实例。所以定义在父类实例上的方法或属性，是无法通过`super`调用的。如果属性定义在父类的原型对象上，`super`就可以取到。

```js
class A {
  constructor() {
    this.p = 2;
  }
}
A.prototype.x = 2;

class B extends A {
  constructor() {
    super();
    console.log(super.x) // 2
  }
  get m() {
    return super.p;
  }
}
let b = new B();
b.m // undefined
```

如果`super`作为对象，用在静态方法之中，这时`super`将指向父类，而不是父类的原型对象。另外，在子类的静态方法中通过`super`调用父类的方法时，方法内部的`this`指向当前的子类，而不是子类的实例。

```js
class A {
  constructor() {
    this.x = 1;
  }
  static print() {
    console.log(this.x);
  }
}
class B extends A {
  constructor() {
    super();
    this.x = 2;
  }
  static m() {
    super.print();
  }
}
B.x = 3;
B.m() // 3
```

类同时有`prototype`属性和`__proto__`属性，因此同时存在两条继承链：

* 子类的`__proto__`属性，表示构造函数的继承，总是指向父类

* 子类`prototype`属性的`__proto__`属性，表示方法的继承，总是指向父类的`prototype`属性。

  ```js
  class A {
  }
  class B extends A {
  }
  B.__proto__ === A // true
  B.prototype.__proto__ === A.prototype // true
  ```

* 子类实例的`__proto__`属性的`__proto__`属性，指向父类实例的`__proto__`属性。也就是说，子类的原型的原型，是父类的原型。

  ```js
  var p1 = new Point(2, 3);
  var p2 = new ColorPoint(2, 3, 'red');
  p2.__proto__ === p1.__proto__ // false
  p2.__proto__.__proto__ === p1.__proto__ // true
  ```



Mixin模式实现多继承

Mixin 指的是多个对象合成一个新的对象，新对象具有各个组成成员的接口。

```js
function mix(...mixins) {
  class Mix {
    constructor() {
      for (let mixin of mixins) {
        copyProperties(this, new mixin()); // 拷贝实例属性
      }
    }
  }
  for (let mixin of mixins) {
    copyProperties(Mix, mixin); // 拷贝静态属性
    copyProperties(Mix.prototype, mixin.prototype); // 拷贝原型属性
  }
  return Mix;
}
function copyProperties(target, source) {
  for (let key of Reflect.ownKeys(source)) {
    if ( key !== 'constructor'
      && key !== 'prototype'
      && key !== 'name'
    ) {
      let desc = Object.getOwnPropertyDescriptor(source, key);
      Object.defineProperty(target, key, desc);
    }
  }
}

class DistributedEdit extends mix(Loggable, Serializable) {
  // ...
}
```





## 十七、模块























