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

使用`static`关键字来声明类的静态成员。

```ts
class Grid {
    static origin = {x: 0, y: 0};
    calculateDistanceFromOrigin(point: {x: number; y: number;}) {
        let xDist = (point.x - Grid.origin.x);
        let yDist = (point.y - Grid.origin.y);
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
    constructor (public scale: number) { }
}

let grid1 = new Grid(1.0);  // 1x scale
let grid2 = new Grid(5.0);  // 5x scale

console.log(grid1.calculateDistanceFromOrigin({x: 10, y: 10}));
console.log(grid2.calculateDistanceFromOrigin({x: 10, y: 10}));
```



### 6.抽象类

抽象类做为其它派生类的基类使用。 它们一般不会直接被实例化。 不同于接口，抽象类可以包含成员的实现细节。 `abstract`关键字是用于定义抽象类和在抽象类内部定义抽象方法。抽象类中的抽象方法不包含具体实现并且必须在派生类中实现。 抽象方法的语法与接口方法相似。 两者都是定义方法签名但不包含方法体。 然而，抽象方法必须包含 `abstract`关键字并且可以包含访问修饰符。

```ts
abstract class Department {

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
department = new Department(); // 错误: 不能创建一个抽象类的实例
department = new AccountingDepartment(); // 允许对一个抽象子类进行实例化和赋值
department.printName();
department.printMeeting();
department.generateReports(); // 错误: 方法在声明的抽象类中不存在
```



### 7.构造函数

类的构造函数`constructor`，使用 `new`创建类实例的时候被调用。

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

let greeter: Greeter;
greeter = new Greeter("world");
console.log(greeter.greet());
```



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

函数是JavaScript应用程序的基础。 它帮助你实现抽象层，模拟类，信息隐藏和模块。 在TypeScript里，虽然已经支持类，命名空间和模块，但函数仍然是主要的定义 *行为*的地方。 TypeScript为JavaScript函数添加了额外的功能，让我们可以更容易地使用。

### 1.为函数定义类型

函数的完整类型书写方式：

```ts
//在函数和返回值类型之前使用( =>)符号
let myAdd: (baseValue: number, increment: number) => number = function(x: number, y: number): number { 
  return x + y; 
};

```

类型推断：如果你在赋值语句的一边指定了类型但是另一边没有类型的话，TypeScript编译器会自动识别出类型。

```ts
// myAdd has the full function type
let myAdd = function(x: number, y: number): number { return x + y; };

// The parameters `x` and `y` have the type number
let myAdd: (baseValue: number, increment: number) => number =
    function(x, y) { return x + y; };
```



### 2.参数

* 可选参数

  avaScript里，每个参数都是可选的，可传可不传。 没传参的时候，它的值就是undefined。 在TypeScript里我们可以在参数名旁使用 `?`实现可选参数的功能。

  ```ts
  function buildName(firstName: string, lastName?: string) {
      if (lastName)
          return firstName + " " + lastName;
      else
          return firstName;
  }
  
  let result1 = buildName("Bob");  // works correctly now
  let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
  let result3 = buildName("Bob", "Adams");  // ah, just right
  ```

  

* 默认参数

  在TypeScript里，我们也可以为参数提供一个默认值当用户没有传递这个参数或传递的值是`undefined`时。 它们叫做有默认初始化值的参数。在参数声明中使用`=`设定默认值。

  ```ts
  function buildName(firstName: string, lastName = "Smith") {
      return firstName + " " + lastName;
  }
  
  let result1 = buildName("Bob");                  // works correctly now, returns "Bob Smith"
  let result2 = buildName("Bob", undefined);       // still works, also returns "Bob Smith"
  let result3 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
  let result4 = buildName("Bob", "Adams");         // ah, just right
  ```

* 剩余参数

  有时，你想同时操作多个参数，或者你并不知道会有多少参数传递进来。 在JavaScript里，你可以使用 `arguments`来访问所有传入的参数。在TypeScript里，你可以使用`...`扩展符号把所有参数收集到一个变量里。

  ```ts
  function buildName(firstName: string, ...restOfName: string[]) {
    return firstName + " " + restOfName.join(" ");
  }
  
  let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
  ```

  

### 3.this与箭头函数

由于TypeScript是JavaScript的超集，TypeScript程序员也需要弄清 `this`工作机制并且当有bug的时候能够找出错误所在。 幸运的是，TypeScript能通知你错误地使用了 `this`的地方。JavaScript里，`this`的值在函数被调用的时候才会指定。 这是个既强大又灵活的特点，但是你需要花点时间弄清楚函数调用的上下文是什么。我们可以在函数被返回时就绑好正确的`this`， 箭头函数能保存函数创建时的 `this`值，而不是调用时的值。

```ts
let deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        // NOTE: the line below is now an arrow function, allowing us to capture 'this' right here
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```



### 4.重载

根据传入参数的不同会返回不同的类型，这种方法是为同一个函数提供多个函数类型定义来进行函数重载。 编译器会根据这个列表去处理函数的调用。重载方法方法名相同，参数类型/个数不同，返回值可以不同。

```ts
let suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x: {suit: string; card: number; }[]): number;
function pickCard(x: number): {suit: string; card: number; };
function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}

let myDeck = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
let pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);

let pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);
```



## 六、泛型

在像C#和Java这样的语言中，可以使用`泛型`来创建可重用的组件，一个组件可以支持多种类型的数据。 这样用户就可以以自己的数据类型来使用组件。

### 1.泛型变量

*类型变量*，它是一种特殊的变量，只用于表示类型而不是值。使用类型变量让程序可以适用于多个类型，这就是泛型。

```ts
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);  // Error: T doesn't have .length
    return arg;
}

interface GenericIdentityFn {
    <T>(arg: T): T;
}
```

除了泛型函数、泛型接口，我们还可以创建泛型类。 注意，无法创建泛型枚举和泛型命名空间。

```ts
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```



### 2.泛型约束

Typescript是强类型语言，在使用没有约束的泛型类型时，相当于操作any类型。这使得编译器不能确定每种类型都具备特定的行为或属性，正因如此编译报错。可以使用接口来约束泛型,使其具备特定行为或属性，这就是泛型约束。

```ts
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // Now we know it has a .length property, so no more error
    return arg;
}
```

在TypeScript使用泛型创建工厂函数时，需要引用构造函数的类类型。

```ts
class BeeKeeper {
    hasMask: boolean;
}

class ZooKeeper {
    nametag: string;
}

class Animal {
    numLegs: number;
}

class Bee extends Animal {
    keeper: BeeKeeper;
}

class Lion extends Animal {
    keeper: ZooKeeper;
}

function createInstance<A extends Animal>(c: new () => A): A {
    return new c();
}

createInstance(Lion).keeper.nametag;  // typechecks!
createInstance(Bee).keeper.hasMask;   // typechecks!
```



## 七、枚举

 TypeScript支持数字的和基于字符串的枚举。

### 1.数字枚举

```ts
enum Direction {
  	//Up使用初始化为 1,其余的成员会从 1开始自动增长
    Up = 1,
    Down,//2
    Left,//3
    Right//4
}
```

如果第一个成员没有初始化那么默认被赋值为0。

### 2.字符串枚举

字符串枚举没有自增长的行为，每个成员都必须用字符串字面量，或另外一个字符串枚举成员进行初始化。

```ts
enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}
```



## 八、类型兼容性

TypeScript里的类型兼容性是基于结构子类型的。 结构类型是一种只使用其成员来描述类型的方式。 它正好与名义（nominal）类型(比如C#或Java)形成对比。TypeScript的结构性子类型是根据JavaScript代码的典型写法来设计的。 因为JavaScript里广泛地使用匿名对象，例如函数表达式和对象字面量，所以使用结构类型系统来描述这些类型比使用名义类型系统更好。

```ts
interface Named {
    name: string;
}

class Person {
    name: string;
}

let p: Named;
// OK, because of structural typing
p = new Person();
```

### 1.对象类型

TypeScript结构化类型系统的基本规则是，如果`x`要兼容`y`，那么`y`至少具有与`x`相同的属性。

```ts
interface Named {
    name: string;
}

let x: Named;
// y's inferred type is { name: string; location: string; }
let y = { name: 'Alice', location: 'Seattle' };
x = y;
```

注意，`y`有个额外的`location`属性，但这不会引发错误。 只有目标类型（这里是`Named`）的成员会被一一检查是否兼容。这个比较过程是递归进行的，检查每个成员及子成员。

### 2.函数类型

```ts
let x = (a: number) => 0;
let y = (b: number, s: string) => 0;

y = x; // OK
x = y; // Error
```

两个函数，要查看`x`是否能赋值给`y`，首先看它们的参数列表。 `x`的每个参数必须能在`y`里找到对应类型的参数。 注意的是参数的名字相同与否无所谓，只看它们的类型。 这里，`x`的每个参数在`y`中都能找到对应的参数，所以允许赋值。第二个赋值错误，因为`y`有个必需的第二个参数，但是`x`并没有，所以不允许赋值。

### 3.枚举类型

枚举类型与数字类型兼容，并且数字类型与枚举类型兼容。不同枚举类型之间是不兼容的。

```ts
enum Status { Ready, Waiting };
enum Color { Red, Blue, Green };

let status = Status.Ready;
status = Color.Green;  // Error
```



### 4.类类型

类与对象字面量和接口差不多，但有一点不同：类有静态部分和实例部分的类型。 比较两个类类型的对象时，只有实例的成员会被比较。 静态成员和构造函数不在比较的范围内。

```ts
class Animal {
    feet: number;
    constructor(name: string, numFeet: number) { }
}

class Size {
    feet: number;
    constructor(numFeet: number) { }
}

let a: Animal;
let s: Size;

a = s;  // OK
s = a;  // OK
```

类的私有成员和受保护成员会影响兼容性。 当检查类实例的兼容时，如果目标类型包含一个私有成员，那么源类型必须包含来自同一个类的这个私有成员。 同样地，这条规则也适用于包含受保护成员实例的类型检查。 这允许子类赋值给父类，但是不能赋值给其它有同样类型的类。



## 九、高级类型

### 1.交叉类型(&)

交叉类型是将多个类型合并为一个类型。使用`&`符号将多个类型合并：

```ts
function extend<T, U>(first: T, second: U): T & U {
    let result = <T & U>{};
    for (let id in first) {
        (<any>result)[id] = (<any>first)[id];
    }
    for (let id in second) {
        if (!result.hasOwnProperty(id)) {
            (<any>result)[id] = (<any>second)[id];
        }
    }
    return result;
}

class Person {
    constructor(public name: string) { }
}
interface Loggable {
    log(): void;
}
class ConsoleLogger implements Loggable {
    log() {
        // ...
    }
}
var jim = extend(new Person("Jim"), new ConsoleLogger());
var n = jim.name;
jim.log();
```



### 2.联合类型(|)

联合类型表示一个值可以是几种类型之一。 使用竖线（ `|`）分隔每个类型，例如： `number | string | boolean`表示一个值可以是 `number`， `string`，或 `boolean`。

```ts
interface Bird {
    fly();
    layEggs();
}

interface Fish {
    swim();
    layEggs();
}

function getSmallPet(): Fish | Bird {
    // ...
}

let pet = getSmallPet();
pet.layEggs(); // okay
pet.swim();    // errors
```



### 3.`typeof`

`typeof`类型保护*只有两种形式能被识别： `typeof v === "typename"`和 `typeof v !== "typename"`

```ts
function padLeft(value: string, padding: string | number) {
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
    if (typeof padding === "string") {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}
```



### 4.`instanceof`

*`instanceof`类型保护*是通过构造函数来细化类型的一种方式。

```ts
interface Padder {
    getPaddingString(): string
}

class SpaceRepeatingPadder implements Padder {
    constructor(private numSpaces: number) { }
    getPaddingString() {
        return Array(this.numSpaces + 1).join(" ");
    }
}

class StringPadder implements Padder {
    constructor(private value: string) { }
    getPaddingString() {
        return this.value;
    }
}

function getRandomPadder() {
    return Math.random() < 0.5 ?
        new SpaceRepeatingPadder(4) :
        new StringPadder("  ");
}

// 类型为SpaceRepeatingPadder | StringPadder
let padder: Padder = getRandomPadder();

if (padder instanceof SpaceRepeatingPadder) {
    padder; // 类型细化为'SpaceRepeatingPadder'
}
if (padder instanceof StringPadder) {
    padder; // 类型细化为'StringPadder'
}
```

### 5.类型别名

使用`type`关键字来声明一个类型别名。

```ts
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    }
    else {
        return n();
    }
}

//泛型
type Tree<T> = {
    value: T;
    left: Tree<T>;
    right: Tree<T>;
}

//字符串字面量
type Easing = "ease-in" | "ease-out" | "ease-in-out";
class UIElement {
    animate(dx: number, dy: number, easing: Easing) {
        if (easing === "ease-in") {
            // ...
        }
        else if (easing === "ease-out") {
        }
        else if (easing === "ease-in-out") {
        }
        else {
            // error! should not pass null or undefined.
        }
    }
}

let button = new UIElement();
button.animate(0, 0, "ease-in");
button.animate(0, 0, "uneasy"); // error: "uneasy" is not allowed here

//交叉类型
type Shape = Square & Rectangle;
//联合类型
type Shape = Square | Rectangle | Circle;
```

### 6.索引类型

使用索引类型，编译器就能够检查使用了动态属性名的代码。

```ts
function pluck<T, K extends keyof T>(o: T, names: K[]): T[K][] {
  return names.map(n => o[n]);
}

interface Person {
    name: string;
    age: number;
}
let person: Person = {
    name: 'Jarid',
    age: 35
};
let strings: string[] = pluck(person, ['name']); // ok, string[]
```



## 十、Symbols

自ECMAScript 2015起，`symbol`成为了一种新的原生类型，就像`number`和`string`一样。

`symbol`类型的值是通过`Symbol`构造函数创建的。Symbols是不可改变且唯一的。像字符串一样，symbols也可以被用做对象属性的键。Symbols也可以与计算出的属性名声明相结合来声明对象的属性和类成员。

```ts
let sym1 = Symbol();

let sym2 = Symbol("key"); // 可选的字符串key
let sym3 = Symbol("key");

sym2 === sym3; // false, symbols是唯一的

let sym = Symbol();

let obj = {
    [sym]: "value"
};

console.log(obj[sym]); // "value"

const getClassNameSymbol = Symbol();

class C {
    [getClassNameSymbol](){
       return "C";
    }
}

let c = new C();
let className = c[getClassNameSymbol](); // "C"
```



除了用户定义的symbols，还有一些已经众所周知的内置symbols。 内置symbols用来表示语言内部的行为。

| 内置Symbol | 说明 |
| ------ | ---- |
| Symbol.hasInstance | 方法，会被instanceof运算符调用。构造器对象用来识别一个对象是否是其实例。 |
| Symbol.isConcatSpreadable | 布尔值，表示当在一个对象上调用Array.prototype.concat时，这个对象的数组元素是否可展开。 |
| Symbol.iterator | 方法，被for-of语句调用。返回对象的默认迭代器。 |
| Symbol.match | 方法，被String.prototype.match调用。正则表达式用来匹配字符串。 |
| Symbol.replace | 方法，被String.prototype.replace调用。正则表达式用来替换字符串中匹配的子串。 |
| Symbol.search | 方法，被String.prototype.search调用。正则表达式返回被匹配部分在字符串中的索引。 |
| Symbol.species | 函数值，为一个构造函数。用来创建派生对象。 |
| Symbol.split | 方法，被String.prototype.split调用。正则表达式来用分割字符串。 |
| Symbol.toPrimitive | 方法，被ToPrimitive抽象操作调用。把对象转换为相应的原始值。 |
| Symbol.toStringTag | 方法，被内置方法Object.prototype.toString调用。返回创建对象时默认的字符串描述。 |
| Symbol.unscopables | 对象，它自己拥有的属性会被with作用域排除在外。 |



## 十一、模块

### 1.导出

任何声明（比如变量，函数，类，类型别名或接口）都能够通过添加`export`关键字来导出。

```ts
export interface StringValidator {
    isAcceptable(s: string): boolean;
}
export const numberRegexp = /^[0-9]+$/;

export class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
```

重命名导出：

```ts
export class ParseIntBasedZipCodeValidator {
    isAcceptable(s: string) {
        return s.length === 5 && parseInt(s).toString() === s;
    }
}

// 导出原先的验证器但做了重命名
export {ZipCodeValidator as RegExpBasedZipCodeValidator} from "./ZipCodeValidator";
```



### 2.导入

模块的导入操作与导出一样简单。 可以使用以下 `import`形式之一来导入其它模块中的导出内容。

```ts
import { ZipCodeValidator } from "./ZipCodeValidator";

let myValidator = new ZipCodeValidator();

//重命名
import { ZipCodeValidator as ZCV } from "./ZipCodeValidator";
let myValidator = new ZCV();

//将整个模块导入到一个变量，并通过它来访问模块的导出部分
import * as validator from "./ZipCodeValidator";
let myValidator = new validator.ZipCodeValidator();
```



### 3.默认导出

每个模块都可以有一个`default`导出。 默认导出使用 `default`关键字标记；并且一个模块只能够有一个`default`导出。 需要使用一种特殊的导入形式来导入 `default`导出，导入默认导出不需要大括号`{}`。

```ts
declare let $: JQuery;
export default $;
```

```ts
import $ from "JQuery";

$("button.continue").html( "Next Step..." );
```



### 4.支持CommonJS和AMD

为了支持CommonJS和AMD的`exports`, TypeScript提供了`export =`语法。若使用`export =`导出一个模块，则必须使用TypeScript的特定语法`import module = require("module")`来导入此模块。

```ts
let numberRegexp = /^[0-9]+$/;
class ZipCodeValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
export = ZipCodeValidator;
```

```ts
import zip = require("./ZipCodeValidator");

// Some samples to try
let strings = ["Hello", "98052", "101"];

// Validators to use
let validator = new zip();

// Show whether each string passed each validator
strings.forEach(s => {
  console.log(`"${ s }" - ${ validator.isAcceptable(s) ? "matches" : "does not match" }`);
});
```





## 十二、命名空间

如何在TypeScript里使用命名空间（之前叫做“内部模块”）来组织你的代码。 就像我们在术语说明里提到的那样，“内部模块”现在叫做“命名空间”。 另外，任何使用 `module`关键字来声明一个内部模块的地方都应该使用`namespace`关键字来替换。

下面的例子里，把所有与验证器相关的类型都放到一个叫做`Validation`的命名空间里。 因为我们想让这些接口和类在命名空间之外也是可访问的，所以需要使用 `export`。 相反的，变量 `lettersRegexp`和`numberRegexp`是实现的细节，不需要导出，因此它们在命名空间外是不能访问的。 

```ts
namespace Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }

    const lettersRegexp = /^[A-Za-z]+$/;
    const numberRegexp = /^[0-9]+$/;

    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
        }
    }

    export class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
}

// Some samples to try
let strings = ["Hello", "98052", "101"];

// Validators to use
let validators: { [s: string]: Validation.StringValidator; } = {};
validators["ZIP code"] = new Validation.ZipCodeValidator();
validators["Letters only"] = new Validation.LettersOnlyValidator();

// Show whether each string passed each validator
for (let s of strings) {
    for (let name in validators) {
        console.log(`"${ s }" - ${ validators[name].isAcceptable(s) ? "matches" : "does not match" } ${ name }`);
    }
}
```

当应用变得越来越大时，我们需要将代码分离到不同的文件中以便于维护。尽管是不同的文件，它们仍是同一个命名空间，并且在使用的时候就如同它们在一个文件中定义的一样。 因为不同文件之间存在依赖关系，所以我们加入了引用标签来告诉编译器文件之间的关联。 

```ts
//Validation.ts

namespace Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }
}
```

```ts
//LettersOnlyValidator.ts

/// <reference path="Validation.ts" />
namespace Validation {
    const lettersRegexp = /^[A-Za-z]+$/;
    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
        }
    }
}
```

```ts
//ZipCodeValidator.ts

/// <reference path="Validation.ts" />
namespace Validation {
    const numberRegexp = /^[0-9]+$/;
    export class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
}
```

```ts
//Test.ts

/// <reference path="Validation.ts" />
/// <reference path="LettersOnlyValidator.ts" />
/// <reference path="ZipCodeValidator.ts" />

// Some samples to try
let strings = ["Hello", "98052", "101"];

// Validators to use
let validators: { [s: string]: Validation.StringValidator; } = {};
validators["ZIP code"] = new Validation.ZipCodeValidator();
validators["Letters only"] = new Validation.LettersOnlyValidator();

// Show whether each string passed each validator
for (let s of strings) {
    for (let name in validators) {
        console.log(`"${ s }" - ${ validators[name].isAcceptable(s) ? "matches" : "does not match" } ${ name }`);
    }
}
```



另一种简化命名空间操作的方法是使用`import q = x.y.z`给常用的对象起一个短的名字。 不要与用来加载模块的 `import x = require('name')`语法弄混了，这里的语法是为指定的符号创建一个别名。 你可以用这种方法为任意标识符创建别名，也包括导入的模块中的对象。

```ts
namespace Shapes {
    export namespace Polygons {
        export class Triangle { }
        export class Square { }
    }
}

import polygons = Shapes.Polygons;
let sq = new polygons.Square(); // Same as "new Shapes.Polygons.Square()"
```



## 十三、装饰器

*装饰器*是一种特殊类型的声明，它能够被附加到类声明，方法， 访问符，属性或参数上。 装饰器使用 `@expression`这种形式，`expression`求值后必须为一个函数，它会在运行时被调用，被装饰的声明信息做为参数传入。

```ts
function sealed(target) {
    // do something with "target" ...
}
```

若要启用实验性的装饰器特性，你必须在命令行或`tsconfig.json`里启用`experimentalDecorators`编译器选项：

```shell
##通过命令行修改ES版本和启用experimentalDecorators 最终也是修改tsconfig.json
tsc --target ES5 --experimentalDecorators
```

```json
//tsconfig.json
{
    "compilerOptions": {
        "target": "ES5",//ES5以上版本
        "experimentalDecorators": true
    }
}
```



### 1.装饰器工厂

如果我们要定制一个修饰器如何应用到一个声明上，我们得写一个装饰器工厂函数。 *装饰器工厂*就是一个简单的函数，它返回一个表达式，以供装饰器在运行时调用。

```ts
function color(value: string) { // 这是一个装饰器工厂
    return function (target) { //  这是装饰器
        // do something with "target" and "value"...
    }
}
```



### 2.装饰器组合

多个装饰器可以同时应用到一个声明上。

```ts
//写在一行上
@f @g x

//写在多行上
@f
@g
x
```

当多个装饰器应用于一个声明上，它们求值方式与[复合函数](http://en.wikipedia.org/wiki/Function_composition)相似。在这个模型下，当复合*f*和*g*时，复合的结果(*f* ∘ *g*)(*x*)等同于*f*(*g*(*x*))。

### 3.类装饰器

类装饰器应用于类构造函数，可以用来监视，修改或替换类定义。 类装饰器不能用在声明文件中( `.d.ts`)，也不能用在任何外部上下文中（比如`declare`的类）。

类装饰器表达式会在运行时当作函数被调用，类的构造函数作为其唯一的参数。

如果类装饰器返回一个值，它会使用提供的构造函数来替换类的声明。

```ts
function classDecorator<T extends { new(...args: any[]): {} }>(constructor: T) {
    return class extends constructor {
        newProperty = "new property";
        hello = "override";
        constructor(...args: any[]) {
            console.log(`Decorator`)
            console.log(args)
            super(...args)
            console.log(this)
        }
    }
}

@classDecorator
class Greeter {
    property = "property";
    hello: string;
    constructor(m: string) {
        console.log(`Greeter`)
        this.hello = m;
    }
}

console.log(new Greeter("world"));
```



### 4.方法装饰器

*方法装饰器*声明在一个方法的声明之前（紧靠着方法声明）。 它会被应用到方法的 *属性描述符*上，可以用来监视，修改或者替换方法定义。

方法装饰器表达式会在运行时当作函数被调用，传入下列3个参数：

* 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。

* 成员的名字。

* 成员的*属性描述符*。

  如果代码输出目标版本小于`ES5`，*属性描述符*将会是`undefined`。

如果方法装饰器返回一个值，它会被用作方法的*属性描述符*。(如果代码输出目标版本小于`ES5`返回值会被忽略。)

```ts
function enumerable(value: boolean) {
    return function (target: Object, properKey: string, descriptor: PropertyDescriptor) {
        console.log(descriptor)
        descriptor.enumerable = value;
    };
}

class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }

    @enumerable(false)
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter = new Greeter(`world`)
console.log(greeter.greet())
```



### 5.访问器装饰器

*访问器装饰器*声明在一个访问器的声明之前（紧靠着访问器声明）。 访问器装饰器应用于访问器的 *属性描述符*并且可以用来监视，修改或替换一个访问器的定义。

访问器装饰器表达式会在运行时当作函数被调用，传入下列3个参数：

* 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。

* 成员的名字。

* 成员的*属性描述符*。

  如果代码输出目标版本小于`ES5`，*Property Descriptor*将会是`undefined`。

如果访问器装饰器返回一个值，它会被用作方法的*属性描述符*。(如果代码输出目标版本小于`ES5`返回值会被忽略。)

```ts
function configurable(value: boolean) {
    return function (target: Object, propertyKey: string, descriptor: PropertyDescriptor) {
        console.log(descriptor)
        descriptor.configurable = value;
    };
}

class Point {
    private _x: number;
    private _y: number;
    constructor(x: number, y: number) {
        this._x = x;
        this._y = y;
    }

    @configurable(false)
    get x() { return this._x; }

    @configurable(false)
    get y() { return this._y; }
}

let point = new Point(1,2);

console.log(point.x);
console.log(point.y);
```



### 6.属性装饰器

属性装饰器表达式会在运行时当作函数被调用，传入下列2个参数：

* 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
* 成员的名字。

```ts
import "reflect-metadata";

const formatMetadataKey = Symbol("format");

function format(formatString: string) {
    return Reflect.metadata(formatMetadataKey, formatString)
}

function getFormat(target: Object, propertyKey: string) {
    return Reflect.getMetadata(formatMetadataKey, target, propertyKey);
}

class Greeter {
    @format("hello, %s")
    greeting: string;

    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        let formatString = getFormat(this, "greeting");
        return formatString.replace("%s", this.greeting);
    }
}

let greeter = new Greeter("Leo")
console.log(greeter.greet());
```



### 7.参数装饰器

参数装饰器表达式会在运行时当作函数被调用，传入下列3个参数：

* 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
* 成员的名字。
* 参数在函数参数列表中的索引。

参数装饰器只能用来监视一个方法的参数是否被传入。

```ts
import "reflect-metadata";

const requiredMetadataKey = Symbol("required");

function required(target: Object, propertyKey: string | symbol, parameterIndex: number) {
    let existingRequiredParameters: number[] = Reflect.getOwnMetadata(requiredMetadataKey, target, propertyKey) || [];
    existingRequiredParameters.push(parameterIndex);
    Reflect.defineMetadata(requiredMetadataKey, existingRequiredParameters, target, propertyKey);
}

function validate(target: Object, propertyName: string, descriptor: PropertyDescriptor) {
    let method = descriptor.value;
    descriptor.value = function () {
        let requiredParameters: number[] = Reflect.getOwnMetadata(requiredMetadataKey, target, propertyName);
        if (requiredParameters) {
            for (let parameterIndex of requiredParameters) {
                if (parameterIndex >= arguments.length || arguments[parameterIndex] === undefined) {
                    throw new Error("Missing required argument.");
                }
            }
        }

        return method?.apply(this, arguments);
    }
}

class Greeter {
    greeting: string;

    constructor(message: string) {
        this.greeting = message;
    }

    @validate
    greet(@required name?: string) {
        return "Hello " + name + ", " + this.greeting;
    }
}

let greeter = new Greeter(`welcome aboard!`);
console.log(greeter.greet(`Jason`));
try {
    console.log(greeter.greet());
} catch (e) {
    console.log(e)
}
```



### 8.元数据metadata

`reflect-metadata`库。

```shell
npm i reflect-metadata --save
```

TypeScript支持为带有装饰器的声明生成元数据。 你需要在命令行或 `tsconfig.json`里启用`emitDecoratorMetadata`编译器选项。

```shell
tsc --target ES5 --experimentalDecorators --emitDecoratorMetadata
```

```json
{
    "compilerOptions": {
        "target": "ES5",
        "experimentalDecorators": true,
        "emitDecoratorMetadata": true
    }
}
```



## 十四、三斜线指令

三斜线指令是包含单个XML标签的单行注释。 注释的内容会做为编译器指令使用。

三斜线指令*仅*可放在包含它的文件的最顶端。 一个三斜线指令的前面只能出现单行或多行注释，这包括其它的三斜线指令。 如果它们出现在一个语句或声明之后，那么它们会被当做普通的单行注释，并且不具有特殊的涵义。

## `/// <reference path="..." />`

三斜线引用告诉编译器在编译过程中要引入的额外的文件。编译器会对输入文件进行预处理来解析所有三斜线引用指令。 在这个过程中，额外的文件会加到编译过程中。

```ts
///<amd-module name='NamedModule'/>
export class C {
}
```

`amd-module`指令允许给编译器传入一个可选的模块名。





