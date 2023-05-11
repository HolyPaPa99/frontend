# Typescript笔记

Typescript是强类型编程语言，是Javascript的超集。TypeScript使用的是结构性类型系统。 当我们比较两种不同的类型时，并不在乎它们从何处而来，如果所有成员的类型都是兼容的，我们就认为它们的类型是兼容的。



## 一、基础数据类型

### 1.boolean

布尔值类型，true/false。

```js
let isDone: boolean = false;
```

### 2.数字

TypeScript里的所有数字都是浮点数。 这些浮点数的类型是 `number`。 除了支持十进制和十六进制字面量，TypeScript还支持ECMAScript 2015中引入的二进制和八进制字面量。

```js
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;
```



### 3.字符串

使用 `string`表示文本数据类型。 和JavaScript一样，可以使用双引号（ `"`）或单引号（`'`）表示字符串。还可以使用*模版字符串*，它可以定义多行文本和内嵌表达式。 这种字符串是被反引号包围（ ```），并且以`${ expr }`这种形式嵌入表达式.。

```js
let name: string = `Gene`;
let age: number = 37;
let sentence: string = `Hello, my name is ${ name }.

I'll be ${ age + 1 } years old next month.`;
```



### 4.数组

有两种方式可以定义数组。

* 可以在元素类型后面接上 `[]`，表示由此类型元素组成的一个数组

  ```js
  let list: number[] = [1, 2, 3];
  ```

  

* 使用数组泛型，`Array<元素类型>`

  ```js
  let list: Array<number> = [1, 2, 3];
  ```

  



### 5.元组Tuple

元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。

```js
// Declare a tuple type
let x: [string, number];
// Initialize it
x = ['hello', 10]; // OK
console.log(x[0]);


// Initialize it incorrectly
x = [10, 'hello']; // Error

```



### 6.枚举

`enum`类型是对JavaScript标准数据类型的一个补充。

```js
enum Color {Red, Green, Blue}
let c: Color = Color.Green;
```



### 7.Any

有时候，我们会想要为那些在编程阶段还不清楚类型的变量指定一个类型。 这些值可能来自于动态的内容，比如来自用户输入或第三方代码库。 这种情况下，我们不希望类型检查器对这些值进行检查而是直接让它们通过编译阶段的检查。 那么我们可以使用 `any`类型来标记这些变量。

```js
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```



### 8.void

某种程度上来说，`void`类型像是与`any`类型相反，它表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 `void`。

```ts
function warnUser(): void {
    console.log("This is my warning message");
}
```

声明一个`void`类型的变量没有什么大用，因为你只能为它赋予`undefined`和`null`：

```ts
let unusable: void = undefined;
```



### 9.Null和Undefined

TypeScript里，`undefined`和`null`两者各自有自己的类型分别叫做`undefined`和`null`。默认情况下`null`和`undefined`是所有类型的子类型。 就是说你可以把 `null`和`undefined`赋值给`number`类型的变量。

```js
// Not much else we can assign to these variables!
let u: undefined = undefined;
let n: null = null;
```



### 10.Never

`never`类型表示的是那些永不存在的值的类型。 例如， `never`类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型； 变量也可能是 `never`类型，当它们被永不为真的类型保护所约束时。

`never`类型是任何类型的子类型，也可以赋值给任何类型；然而，*没有*类型是`never`的子类型或可以赋值给`never`类型（除了`never`本身之外）。 即使 `any`也不可以赋值给`never`。

```ts
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}

// 推断的返回值类型为never
function fail() {
    return error("Something failed");
}

// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
    while (true) {
    }
}
```



### 11.Object

`object`表示非原始类型，也就是除`number`，`string`，`boolean`，`symbol`，`null`或`undefined`之外的类型。

```ts
declare function create(o: object | null): void;

create({ prop: 0 }); // OK
create(null); // OK

create(42); // Error
create("string"); // Error
create(false); // Error
create(undefined); // Error
```



### 12类型推断

类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。 TypeScript会假设你，程序员，已经进行了必须的检查。类型断言有两种形式：

* “尖括号”`<dataType>`

  ```ts
  let someValue: any = "this is a string";
  
  let strLength: number = (<string>someValue).length;
  ```

  

* `as`语法

  ```ts
  let someValue: any = "this is a string";
  
  let strLength: number = (someValue as string).length;
  ```

  

## 二、变量声明

因为TypeScript是JavaScript的超集，所以它本身就支持`var`、`let`和`const`。

```ts
type C = { a: string, b?: number }
function f({ a, b }: C): void {
    // ...
}

function f({ a="", b=0 } = {}): void {
    // ...
}
f();
```



## 三、接口

TypeScript的核心原则之一是对值所具有的*结构*进行类型检查。 它有时被称做“鸭式辨型法”或“结构性子类型化”。 在TypeScript里，接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约。

```ts
interface LabelledValue {
  label: string;
}

function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

### 1.可选属性

接口里的属性不全都是必需的。 在可选属性名字定义的后面加一个`?`符号。

```ts
interface SquareConfig {
  color?: string;
  width?: number;
}
```



### 2.只读属性

一些对象属性只能在对象刚刚创建的时候修改其值。 你可以在属性名前用 `readonly`来指定只读属性:

```ts
interface Point {
    readonly x: number;
    readonly y: number;
}
```



### 3.额外的属性检查

 对象字面量会被特殊对待而且会经过 *额外属性检查*，当将它们赋值给变量或作为参数传递的时候。 如果一个对象字面量存在任何“目标类型”不包含的属性时，你会得到一个错误。

```ts
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    // ...
}
// error: 'colour' not expected in type 'SquareConfig'
let mySquare = createSquare({ colour: "red", width: 100 });
```

如果 `SquareConfig`带有上面定义的类型的`color`和`width`属性，并且*还会*带有任意数量的其它属性，那么我们可以这样定义它：

```ts
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
```

### 4.函数类型

接口能够描述JavaScript中对象拥有的各种各样的外形。 除了描述带有属性的普通对象外，接口也可以描述函数类型。为了使用接口表示函数类型，我们需要给接口定义一个调用签名。 它就像是一个只有参数列表和返回值类型的函数定义。参数列表里的每个参数都需要名字和类型。

```ts
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  let result = source.search(subString);
  return result > -1;
}s
```



### 5.可索引类型

可索引类型具有一个 *索引签名*，它描述了对象索引的类型，还有相应的索引返回值类型。TypeScript支持两种索引签名：字符串和数字。 可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型。  这是因为当使用 `number`来索引时，JavaScript会将它转换成`string`然后再去索引对象。 也就是说用 `100`（一个`number`）去索引等同于使用`"100"`（一个`string`）去索引，因此两者需要保持一致。

```js
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];


```

```js
class Animal {
    name: string;
}
class Dog extends Animal {
    breed: string;
}

// 错误：使用数值型的字符串索引，有时会得到完全不同的Animal!
interface NotOkay {
    [x: number]: Animal;
    [x: string]: Dog;
}
```



### 6.类类型

与C#或Java里接口的基本作用一样，TypeScript也能够用它来明确的强制一个类去符合某种契约。

```ts
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```

用构造器签名去定义一个接口不能直接定义一个类去实现这个接口：

```ts
interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}
interface ClockInterface {
    tick();
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
    return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("beep beep");
    }
}
class AnalogClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("tick tock");
    }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```



### 7.继承接口

和类一样，接口也可以相互继承。 这让我们能够从一个接口里复制成员到另一个接口里，可以更灵活地将接口分割到可重用的模块里。一个接口可以继承多个接口，创建出多个接口的合成接口。

```ts
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```



### 8.混合类型

```ts
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```



### 9.接口继承类

当接口继承了一个类类型时，它会继承类的成员但不包括其实现。 就好像接口声明了所有类中存在的成员，但并没有提供具体实现一样。 接口同样会继承到类的private和protected成员。 这意味着当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其子类所实现（implement）。

```ts
class Control {
    private state: any;
}

interface SelectableControl extends Control {
    select(): void;
}

class Button extends Control implements SelectableControl {
    select() { }
}

class TextBox extends Control {
    select() { }
}

// 错误：“Image”类型缺少“state”属性。
class Image implements SelectableControl {
    select() { }
}

class Location {

}
```



## 四、类

### 1.类定义

和其他面向对象语言一样，通过`class`关键字声明定义一个类，通过`new`关键字创建一个类的实例。

```ts
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter = new Greeter("world");
```



### 2.继承

基于类的程序设计中一种最基本的模式是允许使用继承来扩展现有的类。类从基类中继承了属性和方法。派生类通常被称作 *子类*，基类通常被称作 *超类*。子类通过`extends`关键字声明继承某个超类，在子类的对象方法中可以通过`super`关键字调用超类的方法。Typescript里只支持类的单继承。

```js
class Animal {
    name: string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 5) {
        console.log("Slithering...");
        super.move(distanceInMeters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45) {
        console.log("Galloping...");
        super.move(distanceInMeters);
    }
}

let sam = new Snake("Sammy the Python");
let tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
```



### 3.修饰符

TypeScript使用的是结构性类型系统。 当我们比较两种不同的类型时，并不在乎它们从何处而来，如果所有成员的类型都是兼容的，我们就认为它们的类型是兼容的。然而，当我们比较带有 `private`或 `protected`成员的类型的时候，情况就不同了。 如果其中一个类型里包含一个 `private`成员，那么只有当另外一个类型中也存在这样一个 `private`成员， 并且它们都是来自同一处声明时，我们才认为这两个类型是兼容的。 对于 `protected`成员也使用这个规则。也就是说，如果一个类的`private`或`protected`成员是继承来的， 那么另一个类的同类型`private`或`protected`成员必须也是继承自同一个超类。

* public

  在TypeScript里，类成员访问控制默认为 `public`。

  ```ts
  class Animal {
      public name: string;
      public constructor(theName: string) { this.name = theName; }
      public move(distanceInMeters: number) {
          console.log(`${this.name} moved ${distanceInMeters}m.`);
      }
  }
  ```

  

* private

  当成员被标记成 `private`时，它就不能在声明它的类的外部访问。

  ```ts
  class Animal {
      private name: string;
      constructor(theName: string) { this.name = theName; }
  }
  
  class Rhino extends Animal {
      constructor() { super("Rhino"); }
  }
  
  class Employee {
      private name: string;
      constructor(theName: string) { this.name = theName; }
  }
  
  let animal = new Animal("Goat");
  let rhino = new Rhino();
  let employee = new Employee("Bob");
  
  animal = rhino;
  animal = employee; // 错误: Animal 与 Employee 不兼容.
  ```

  

* protected

  `protected`修饰符与 `private`修饰符的行为很相似，但有一点不同， `protected`成员在派生类中仍然可以访问。构造函数也可以被标记成 `protected`。 这意味着这个类不能在包含它的类外被实例化，但是能被继承。

  ```ts
  class Person {
      protected name: string;
      constructor(name: string) { this.name = name; }
  }
  
  class Employee extends Person {
      private department: string;
  
      constructor(name: string, department: string) {
          super(name)
          this.department = department;
      }
  
      public getElevatorPitch() {
          return `Hello, my name is ${this.name} and I work in ${this.department}.`;
      }
  }
  
  let howard = new Employee("Howard", "Sales");
  console.log(howard.getElevatorPitch());
  console.log(howard.name); // 错误
  ```

  

* readonly

  `readonly`关键字将属性设置为只读的。 只读属性必须在声明时或构造函数里被初始化。

  ```ts
  class Octopus {
      readonly name: string;
      readonly numberOfLegs: number = 8;
      constructor (theName: string) {
          this.name = theName;
      }
  }
  let dad = new Octopus("Man with the 8 strong legs");
  dad.name = "Man with the 3-piece suit"; // 错误! name 是只读的.
  ```

  

### 4.存储器get/set

TypeScript支持通过getters/setters来截取对对象成员的访问。 它能帮助你有效的控制对对象成员的访问。只带有 `get`不带有 `set`的存取器自动被推断为 `readonly`。

```ts
let passcode = "secret passcode";

class Employee {
    private _fullName: string;

    get fullName(): string {
        return this._fullName;
    }

    set fullName(newName: string) {
        if (passcode && passcode == "secret passcode") {
            this._fullName = newName;
        }
        else {
            console.log("Error: Unauthorized update of employee!");
        }
    }
}

let employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```



### 5. 静态属性



### 6.抽象类





### 7.构造函数



### 8.实现接口

类通过`implements`关键字声明实现某个接口，一个类可以实现多个接口。

```ts
interface Animal {
    name: string;
}

interface Dog {
    bark:()=> void;
}

class Husky implements Animal, Dog {
    
    name: string;
    constructor(name: string){
        this.name = name;
    }

    bark(){
        console.log(`${this.name}: Woow, Woow!`)
    };
}

let hudy = new Husky(`Hudy`);
hudy.bark();
```





## 五、函数





## 六、泛型





## 七、枚举







## 八、













