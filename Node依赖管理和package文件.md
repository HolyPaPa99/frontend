## 1.Node依赖包管理

### 1.1管理器

依赖包通常是项目所依赖的第三方库、工具和框架等资源，它们能够帮助我们减少重复开发、提高效率并且确保项目可以正确的运行。目前比较常见的包管理器有 npm 和 Yarn，npm 是 Node.js 自带的包管理器，它可以安装、共享和分发 node.js 模块。yarn 是FaceBook推出的，主要是针对 npm 早期版本的很多问题。但 npm 也意识到了来自竞争对手的强大压力，因此在 5.x 开始逐个优化看齐。从 5.x 开始就已经和 yarn 不分伯仲了，因此如何选择多数看是否有历史包袱。如果是新项目的话，就看程序员个人的喜好了。

### 1.2`npm`常用命令(v9.6.5)

参考文档：https://docs.npmjs.com/cli/v9/commands

#### 1.2.1升级`npm`到最新版本

```shell
npm install npm@latest -g
```



#### 1.2.2`npm`配置管理

* `set`

  设置`npm`配置项有两种书写方式：

  ```shell
  npm config set key=value [key=value...]
  npm set key=value [key=value...]
  ```

  ```shell
  #设置作者名称
  npm set init-author-name 'Your name'
  #设置作者邮箱
  npm set init-author-email 'Your email'
  #设置作者主页URL
  npm set init-author-url 'http://yourdomain.com'
  #设置license
  npm set init-license 'MIT'
  #设置加入模块时，package.json将记录模块的确切版本，而不是一个可选的版本范围
  npm set save-exact true
  ```

* get

  使用`key`查询并显示`npm`配置

  ```shell
  npm config get [key ...]
  npm get [key ...]
  ```

  如果不提供查询的`key`那么就和执行`npm config list`命令一样。

* list

  显示所有`npm`配置。默认显示用户设置的配置，加上`-l`所有的配置。使用`--json`参数以JSON格式显示所有配置。

  ```shell
  npm config list
  npm config list -l
  npm config list --json
  ```

  

* delete

  删除指定的配置。

  ```shell
  npm config delete key [key ...]
  ```

  

* eidit

  编辑配置文件。使用`--global`参数编辑全局配置文件。

  ```shell
  npm config edit
  npm config edit --global
  ```

  

* fix

  修复无效配置项。

  ```shell
  npm config fix
  ```

  

#### 1.2.3初始化生成`package.json`文件

```shell
npm init
```

在这个过程中会向用户提问一系列问题，如果你觉得不用修改默认配置，一路回车就可以了。

如果使用了`-f`（代表`force`）、`-y`（代表`yes`），则跳过提问阶段，直接生成一个新的`package.json`文件。

```shell
npm init -y
```



### 1.3`npm`加载机制





## 2.package.json

