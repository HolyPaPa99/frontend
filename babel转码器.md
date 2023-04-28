## 一、Babel

### 1.介绍

Babel是一个Javascript编译器。

用通俗的话解释就是它主要用于将高版本的JavaScript代码转为向后兼容的JS代码，从而能让我们的代码运行在更低版本的浏览器或者其他的环境中。

babel5及之前是一个包含CLI工具+编译器+转换器的集合工具包；babel6之后进行了拆分，集合包被分成多个包：

- babel-cli，其中包含babel命令行界面
- babel-core，包括了Node有关的API和require钩子
- babel-polyfill，可以建立一个完整的ES2015环境

Babel7用了npm的private scope，把所有的包都挂载`@babel`下，通过`@babel/xxx`来安装，不用在node_modules下看到一堆的babel-xxx包。

Babel的运行方式总共可以分为三个阶段：解析（parsing）、转换（transforming）和生成（generating）；负责解析阶段的插件是`@babel/parser`，其作用就是将源码解析成AST；而负责生成阶段的插件是`@babel/generator`，其作用就是将转好好的AST重新生成代码。

### 2.配置文件

Babel 有两种并行的配置文件方式，可以一起使用，也可以单独使用。

- 项目范围的配置
  - `babel.config.*` 文件，可用如下不同扩展名： `.json`, `.js`, `.cjs`, `.mjs`。
- 相对文件的配置
  - `.babelrc.*` 文件，可用如下不同扩展名： `.json`, `.js`, `.cjs`, `.mjs`。
  - 不带扩展名的 `.babelrc`。
  - 带有 `"babel"` 属性的 `package.json` 文件。

其中，不同配置文件内容格式也不相同：

- `babel.config.json` 和 `.babelrc.json` 被解析为 JSON5，并且应该包含一个 Babel 接受的选项格式匹配的对象。 它们从 `v7.7.0` 开始支持。出于兼容性原因，`.babelrc` 是 `.babelrc.json` 的别名。

  我们建议尽可能使用这种文件类型： 如果你的复杂配置有条件表达式或是在构建时计算的，JS 配置文件则非常方便。 然而，缺点是 JS 配置的静态可分析性较差， 因此对可缓存性、代码检测、IDE 自动完成等有负面影响。 由于 `babel.config.json` 和 `.babelrc.json` 是静态 JSON 文件， 因此它允许其他使用 Babel 的工具，如绑定器，安全地缓存 Babel 的结果， 这可能会带来巨大的构建性能优势。

- `babel.config.cjs` 和 `.babelrc.cjs` 允许你使用 `module.exports` 将配置定义为 CommonJS。 它们从 `v7.7.0` 开始支持。

- `babel.config.mjs` 和 `.babelrc.mjs` 使用原生 ECMAScript 模块。它们在 Node.js 13.2+（或使用 `--experimental-modules` 标志的更早版本）被支持。 请记住，原生 ECMAScript 模块是异步的（这就是 `import()` 总是返回 promise 的原因！） 因此，同步调用 Babel 时将抛出 `.mjs` 配置文件。 它们从 `v7.8.0` 开始支持。

- 当你的 `package.json` 文件包含 `"type": "module"` 选项时， `babel.config.js` 和 `.babelrc.js` 的行为类似 `.mjs`， 否则它们与 `.cjs` 文件完全相同。

JSON配置方式：

```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns": "entry",
        "corejs": "3.22"
      }
    ]
  ],
  "plugins": []
}
```



关于配置项详细参考：https://babel.docschina.org/docs/en/options/



### 3.预设和插件

Babel的插件大致可以分为`语法插件`和`转换插件`：

- 语法插件：作用于解析阶段，使得babel能够解析更多的语法，官方的语法插件以`babel-plugin-syntax`开头。
- 转换插件：在转换这一步把源码转换并输出，官方的转换插件以`babel-plugin-transform`（正式）`或 babel-plugin-proposal`（提案）开头。

Babel官网提供了近一百个插件，但是如果我们的代码中一个一个的配置插件就需要对每一个插件有所了解，这样必然会耗费大量的时间精力；为此，Babel提供了预设（presets）的概念，意思就是预先设置好的一系列插件包；这就相当于肯德基中的套餐，将众多产品进行搭配组合，适合不同的人群需要；总有一款适合我们的套餐。

我们实际会用到的预设有以下：

- @babel/preset-env

  @babel/preset-env是一个智能预设，可让您使用最新的JavaScript，而无需微观管理目标环境所需的语法转换（以及可选的浏览器polyfill）。这都使您的生活更轻松，JavaScript包更小！

- @babel/preset-flow

- @babel/preset-react

- @babel/preset-typescript

更多预设和插件使用参考：https://babel.docschina.org/docs/en





| 参考目录                                                     |
| ------------------------------------------------------------ |
| https://babel.docschina.org/docs/en/                         |
| https://www.zhihu.com/tardis/zm/art/326824078?source_id=1005 |



