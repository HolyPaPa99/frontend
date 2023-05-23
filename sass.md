# Sass笔记

Sass 是一款强化 CSS 的辅助工具，它在 CSS 语法的基础上增加了变量 (variables)、嵌套 (nested rules)、混合 (mixins)、导入 (inline imports) 等高级功能，这些拓展令 CSS 更加强大与优雅。使用 Sass 以及 Sass 的样式库（如 [Compass](http://compass-style.org/)）有助于更好地组织管理样式文件，以及更高效地开发项目。

- 完全兼容 CSS3
- 在 CSS 基础上增加变量、嵌套 (nesting)、混合 (mixins) 等功能
- 通过[函数](http://sass-lang.com/docs/yardoc/Sass/Script/Functions.html)进行颜色值与属性值的运算
- 提供控制指令 (control directives)等高级功能
- 自定义输出格式

官方文档：https://sass-lang.com/documentation/

中文文档：https://www.sass.hk/docs/

## 一、格式

* `.sass`

  Sass 语法格式，被称为缩进格式 (Indented Sass) 通常简称 "Sass"，是一种简化格式。它使用 “缩进” 代替 “花括号” 表示属性属于某个选择器，用 “换行” 代替 “分号” 分隔属性，很多人认为这样做比 SCSS 更容易阅读，书写也更快速。这种格式以 `.sass` 作为拓展名。

* `.scss`

  这种格式仅在 CSS3 语法的基础上进行拓展，所有 CSS3 语法在 SCSS 中都是通用的，同时加入 Sass 的特色功能。此外，SCSS 也支持大多数 CSS hacks 写法以及浏览器前缀写法 (vendor-specific syntax)，以及早期的 IE 滤镜写法。这种格式以 `.scss` 作为拓展名。

文件编码格式：

Sass 首先检查 Unicode byte order mark，然后是 `@charset` 声明，最后是 Ruby string encoding，假如都没有检测到，默认使用 UTF-8 编码。与 CSS 相同，使用 `@charset` 可以声明特定的编码格式。在样式文件的起始位置（前面没有任何空白与注释）插入 `@charset "encoding-name"`， Sass 将会按照给出的编码格式编译文件。注意所使用的编码格式必须可转换为 Unicode 字符集。



## 二、CSS功能拓展



### 1.嵌套

Sass 允许将一套 CSS 样式嵌套进另一套样式中，内层的样式将它外层的选择器作为父选择器。

```scss
#main p {
  color: #00ff00;
  width: 97%;

  .redbox {
    background-color: #ff0000;
    color: #000000;
  }
}
```

编译为

```css
#main p {
  color: #00ff00;
  width: 97%; 
}
#main p .redbox {
  background-color: #ff0000;
  color: #000000; 
}
```



### 2.父选择器 `&`

在嵌套 CSS 规则时，有时也需要直接使用嵌套外层的父选择器，可以用 `&` 代表嵌套规则外层的父选择器。

```scss
a {
  font-weight: bold;
  text-decoration: none;
  &:hover { 
    text-decoration: underline; 
  }
  body.firefox & { 
    font-weight: normal; 
  }
}
```

编译为

```css
a {
  font-weight: bold;
  text-decoration: none; 
}
a:hover {
  text-decoration: underline; 
}
body.firefox a {
  font-weight: normal; 
}
```



### 3.属性嵌套

有些 CSS 属性遵循相同的命名空间 (namespace)，比如 `font-family, font-size, font-weight` 都以 `font` 作为属性的命名空间。为了便于管理这样的属性，同时也为了避免了重复输入，Sass 允许将属性嵌套在命名空间中，例如：

```scss
.funky {
  font: {
    family: fantasy;
    size: 30em;
    weight: bold;
  }
}
```

编译为

```css
.funky {
  font-family: fantasy;
  font-size: 30em;
  font-weight: bold; 
}
```



### 4.占位符选择器 `%`

Sass 额外提供了一种特殊类型的选择器：占位符选择器 (placeholder selector)。与常用的 id 与 class 选择器写法相似，只是 `#` 或 `.` 替换成了 `%`。必须通过`@extend`指令调用。



## 三、注释

Sass 支持标准的 CSS 多行注释 `/* */`，以及单行注释 `//`，前者会 被完整输出到编译后的 CSS 文件中，而后者则不会，例如：

```scss
/* This comment is
 * several lines long.
 * since it uses the CSS comment syntax,
 * it will appear in the CSS output. */
body { color: black; }

// These comments are only one line long each.
// They won't appear in the CSS output,
// since they use the single-line comment syntax.
a { color: green; }
```

编译为

```css
/* This comment is
 * several lines long.
 * since it uses the CSS comment syntax,
 * it will appear in the CSS output. */
body {
  color: black; }

a {
  color: green; }
```

将 `!` 作为多行注释的第一个字符表示在压缩输出模式下保留这条注释并输出到 CSS 文件中，通常用于添加版权信息。

## 四、SassScript

### 1.变量

SassScript 最普遍的用法就是变量，变量以美元符号开头，赋值方法与 CSS 属性的写法一样：

```scss
$width: 5em;
```

直接使用即调用变量：

```css
#main {
  width: $width;
}
```

变量支持块级作用域，嵌套规则内定义的变量只能在嵌套规则内使用（局部变量），不在嵌套规则内定义的变量则可在任何地方使用（全局变量）。将局部变量转换为全局变量可以添加 `!global` 声明：

```scss
#main {
  $width: 5em !global;
  width: $width;
}

#sidebar {
  width: $width;
}
```

编译为

```css
#main {
  width: 5em;
}

#sidebar {
  width: 5em;
}
```



### 2.数据类型

SassScript 支持 6 种主要的数据类型：

- 数字，`1, 2, 13, 10px`
- 字符串，有引号字符串与无引号字符串，`"foo", 'bar', baz`
- 颜色，`blue, #04a3f9, rgba(255,0,0,0.5)`
- 布尔型，`true, false`
- 空值，`null`
- 数组 (list)，用空格或逗号作分隔符，`1.5em 1em 0 2em, Helvetica, Arial, sans-serif`
- maps, 相当于 JavaScript 的 object，`(key1: value1, key2: value2)`

SassScript 也支持其他 CSS 属性值，比如 Unicode 字符集，或 `!important` 声明。然而Sass 不会特殊对待这些属性值，一律视为无引号字符串。









