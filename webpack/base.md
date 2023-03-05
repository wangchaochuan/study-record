# webpack知识图谱

![在这里插入图片描述](https://img-blog.csdnimg.cn/41b4d919579d4bf6988112c1a87c2eae.png#pic_center)
## webpack概念
### 概念
 `Webpack`  是一种用于构建 `JavaScript` 应用程序的**静态模块打包器**，它能够以一种相对一致且开放的处理方式，加载应用中的所有资源文件（图片、`CSS`、视频、字体文件等），并将其合并打包成浏览器兼容的 `Web` 资源文件。
 ### 功能
 - 模块的打包：通过打包整合不同的模块文件保证各模块之间的引用和执行
 - 代码编译：通过丰富的 `loader` 可以将不同格式文件如 `.sass/.vue/.jsx`转译为浏览器可以执行的文件
 - 扩展功能：通过社区丰富的 `plugin` 可以实现多种强大的功能，例如代码分割、代码混淆、代码压缩、按需加载等等
##  entry
### 单入口
将源文件加入到 webpack 构建流程，可以是单入口

```javascript
module.exports = {
  entry: `./index.js`,
}
```
构建包名称 `[name]`为 `main`
### 多入口
也可以是多入口
```javascript
module.exports = {
  entry: { 
    "index": `./index.js`,
    "product":`./product.js`,
  },
}
```
`key:value` 键值对的形式：
- key：构建包名称，即 `[name]` ，在这里为 `index`
- value：入口路径

入口决定 `webapck` 从哪个模块开始生成依赖关系图（构建包），每一个入口文件都对应着一个依赖关系图。

### 动态配置入口文件
#### 动态打包所有子项目
当构建项目包含多个子项目时，每次增加一个子系统都需要将入口文件写入 `webpack`  配置文件中，其实我们让 `webpack`  动态获取入口文件

```javascript
// 使用 glob 等工具使用若干通配符，运行时获得 entry 的条目
module.exports = {
  entry: glob.sync('./project/**/index.js').reduce((acc, path) => {
    const entry = path.replace('/index.js', '')
    acc[entry] = path
    return acc
  }, {}),
}
```
则会将所有匹配  `./project/**/index.js`  的文件作为入口文件进行打包，如果你想要增加一个子项目，仅仅需要在  `project`  创建一个子项目目录，并创建一个  `index.js`  作为入口文件即可。

这种方式比较适合入口文件不集中且较多的场景。

#### 动态打包某一子项目
在构建多系统应用或组件库时，我们每次打包可能仅仅需要打包某一模块，此时，可以通过命令行的形式请求打印某一模块

```shell
npm run build --project components
```

在打包的时候解析命令行参数：
```javascript
// 解析命令行参数
const argv = require('minimist')(process.argv.slice(2))
// 项目
const project = argv['project'] || 'index'
```

然后配置入口：

```javascript
module.exports = {
  entry: { 
    "index": `./${project}/index.js`,
  } 
}
```

相当于：

```javascript
module.exports = {
  entry: { 
    "index": `./components/index.js`,
  } 
}
```

##  output
用于告知  `webpack`  如何构建编译后的文件，可以自定义输出文件的位置和名称:

```javascript
module.exports = {
  output: { 
    // path 必须为绝对路径
    // 输出文件路径
    path: path.resolve(__dirname, '../../dist/build'),
    // 包名称
    filename: "[name].bundle.js",
    // 或使用函数返回名(不常用)
    // filename: (chunkData) => {
    //   return chunkData.chunk.name === 'main' ? '[name].js': '[name]/[name].js';
    // },
    // 块名，公共块名(非入口)
    chunkFilename: '[name].[chunkhash].bundle.js',
    // 打包生成的 index.html 文件里面引用资源的前缀
    // 也为发布到线上资源的 URL 前缀
    // 使用的是相对路径，默认为 ''
    publicPath: '/', 
  }
}
```
### 文件指纹策略
对于我们开发的每一个应用，浏览器都会对静态资源进行缓存，如果我们更新了静态资源，而没有更新静态资源名称（或路径），浏览器就可能因为缓存的问题获取不到更新的资源。

`Webpack`  文件指纹策略是将文件名后面加上  `hash`  值。特别在使用  `CDN`  的时候，缓存是它的特点与优势，但如果打包的文件名，没有  `hash`  后缀的话，你肯定会被缓存折磨的够呛

在定义包名称（例如  `chunkFilename`  、  `filename` ），我们一般会用到哈希值，不同的哈希值使用的场景不同

##### hash
`build-specific` ， 哈希值对应每一次构建（  `Compilation`  ），即每次编译都不同，即使文件内容都没有改变，并且所有的资源都共享这一个哈希值，此时，浏览器缓存就没有用了，**可以用在开发环境，生产环境不适用**。

##### chunkhash
`chunk-specific` ， 哈希值对应于  `webpack`  每个入口点，每个入口都有自己的哈希值。如果在某一入口文件创建的关系依赖图上存在文件内容发生了变化，那么相应入口文件的  `chunkhash`  才会发生变化，**适用于生产环境**

##### contenthash
`content-specific` ，根据包内容计算出的哈希值，只要包内容不变， `contenthash`  就不变，**适用于生产环境**

`webpack`  也允许哈希的切片。如果你写 ` [hash:8] ` ，那么它会获取哈希值的前 8 位。

### 打包成库
当使用  `webapck`  构建一个可以被其它模块引用的库时：

```javascript
module.exports = {
  output: { 
    // path 必须为绝对路径
    // 输出文件路径
    path: path.resolve(__dirname, '../../dist/build'),
    // 包名称
    filename: "[name].bundle.js",
    // 块名，公共块名(非入口)
    chunkFilename: '[name].[chunkhash].bundle.js',
    // 打包生成的 index.html 文件里面引用资源的前缀
    // 也为发布到线上资源的 URL 前缀
    // 使用的是相对路径，默认为 ''
    publicPath: '/', 
    // 一旦设置后该 bundle 将被处理为 library
    library: 'webpackNumbers',
    // export 的 library 的规范，有支持 var, this, commonjs,commonjs2,amd,umd
    libraryTarget: 'umd',
  }
}
```

## 配置模式 mode
设置  `mode`  ，可以让 ` webpack`  自动调起相应的内置优化

```javascript
module.exports = {
  // 可以是 none、development、production
  // 默认为 production
  mode: 'production'
}
```

或在命令行里配置：
```shell
"build:prod": "webpack --config config/webpack.prod.config.js --mode production"
```

在设置了  `mode`  之后， `webpack`  会同步配置  `process.env.NODE_ENV`  为  `development`  或  `production`  。

### production
配置：

```javascript
// webpack.prod.config.js
module.exports = {
  mode: 'production',
}
```

相当于默认内置了：

```javascript
// webpack.prod.config.js
module.exports = {
  performance: {
    // 性能设置,文件打包过大时，会报警告
    hints: 'warning'
  },
  output: {
    // 打包时，在包中不包含所属模块的信息的注释
    pathinfo: false
  },
  optimization: {
    // 不使用可读的模块标识符进行调试
    namedModules: false,
    // 不使用可读的块标识符进行调试
    namedChunks: false,
    // 设置 process.env.NODE_ENV 为 production
    nodeEnv: 'production',
    // 标记块是否是其它块的子集
    // 控制加载块的大小（加载较大块时，不加载其子集）
    flagIncludedChunks: true,
    // 标记模块的加载顺序，使初始包更小
    occurrenceOrder: true,
    // 启用副作用
    sideEffects: true,
    // 确定每个模块的使用导出，
    // 不会为未使用的导出生成导出
    // 最小化的消除死代码
    // optimization.usedExports 收集的信息将被其他优化或代码生成所使用
    usedExports: true,
    // 查找模块图中可以安全的连接到其它模块的片段
    concatenateModules: true,
    // SplitChunksPlugin 配置项
    splitChunks: {
      // 默认 webpack4 只会对按需加载的代码做分割
      chunks: 'async',
      // 表示在压缩前的最小模块大小,默认值是30kb
      minSize: 30000,
      minRemainingSize: 0,
      // 旨在与HTTP/2和长期缓存一起使用 
      // 它增加了请求数量以实现更好的缓存
      // 它还可以用于减小文件大小，以加快重建速度。
      maxSize: 0,
      // 分割一个模块之前必须共享的最小块数
      minChunks: 1,
      // 按需加载时的最大并行请求数
      maxAsyncRequests: 6,
      // 入口的最大并行请求数
      maxInitialRequests: 4,
      // 界定符
      automaticNameDelimiter: '~',
      // 块名最大字符数
      automaticNameMaxLength: 30,
      cacheGroups: { // 缓存组
        vendors: {
          test: /[\\/]node_modules[\\/]/,
          priority: -10
        },
        default: {
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true
        }
      }
    },
    // 当打包时，遇到错误编译，将不会把打包文件输出
    // 确保 webpack 不会输入任何错误的包
    noEmitOnErrors: true,
    checkWasmTypes: true,
    // 使用 optimization.minimizer || TerserPlugin 来最小化包
    minimize: true,
  },
  plugins: [
    // 使用 terser 来优化 JavaScript
    new TerserPlugin(/* ... */),
    // 定义环境变量
    new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("production") }),
    // 预编译所有模块到一个闭包中，提升代码在浏览器中的执行速度
    new webpack.optimize.ModuleConcatenationPlugin(),
    // 在编译出现错误时，使用 NoEmitOnErrorsPlugin 来跳过输出阶段。
    // 这样可以确保输出资源不会包含错误
    new webpack.NoEmitOnErrorsPlugin()
  ]
}

```

### development
```javascript
// webpack.dev.config.js
module.exports = {
  mode: 'development',
}
```
相当于默认内置了：

```javascript
// webpack.dev.config.js
module.exports = {
  devtool: 'eval',
  cache: true,
  performance: {
    // 性能设置,文件打包过大时，不报错和警告，只做提示
    hints: false
  },
  output: {
    // 打包时，在包中包含所属模块的信息的注释
    pathinfo: true
  },
  optimization: {
    // 使用可读的模块标识符进行调试
    namedModules: true,
    // 使用可读的块标识符进行调试
    namedChunks: true,
    // 设置 process.env.NODE_ENV 为 development
    nodeEnv: 'development',
    // 不标记块是否是其它块的子集
    flagIncludedChunks: false,
    // 不标记模块的加载顺序
    occurrenceOrder: false,
    // 不启用副作用
    sideEffects: false,
    usedExports: false,
    concatenateModules: false,
    splitChunks: {
      hidePathInfo: false,
      minSize: 10000,
      maxAsyncRequests: Infinity,
      maxInitialRequests: Infinity,
    },
    // 当打包时，遇到错误编译，仍把打包文件输出
    noEmitOnErrors: false,
    checkWasmTypes: false,
    // 不使用 optimization.minimizer || TerserPlugin 来最小化包
    minimize: false,
    removeAvailableModules: false
  },
  plugins: [
    // 当启用 HMR 时，使用该插件会显示模块的相对路径
    // 建议用于开发环境
    new webpack.NamedModulesPlugin(),
    // webpack 内部维护了一个自增的 id，每个 chunk 都有一个 id。
    // 所以当增加 entry 或者其他类型 chunk 的时候，id 就会变化，
    // 导致内容没有变化的 chunk 的 id 也发生了变化
    // NamedChunksPlugin 将内部 chunk id 映射成一个字符串标识符（模块的相对路径）
    // 这样 chunk id 就稳定了下来
    new webpack.NamedChunksPlugin(),
    // 定义环境变量
    new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("development") }),
  ]
}
```
### none
不进行任何默认优化选项。

配置：

```javascript
// webpack.base.config.js
module.exports = {
  mode: 'none',
}
```

相当于默认内置了：

```javascript
// webpack.base.config.js
module.exports = {
  performance: {
   // 性能设置,文件打包过大时，不报错和警告，只做提示
   hints: false
  },
  optimization: {
    // 不标记块是否是其它块的子集
    flagIncludedChunks: false,
    // 不标记模块的加载顺序
    occurrenceOrder: false,
    // 不启用副作用
    sideEffects: false,
    usedExports: false,
    concatenateModules: false,
    splitChunks: {
      hidePathInfo: false,
      minSize: 10000,
      maxAsyncRequests: Infinity,
      maxInitialRequests: Infinity,
    },
    // 当打包时，遇到错误编译，仍把打包文件输出
    noEmitOnErrors: false,
    checkWasmTypes: false,
    // 不使用 optimization.minimizer || TerserPlugin 来最小化包
    minimize: false,
  },
  plugins: []
}
```

### 三种模式对比

|选项| 描述 |
|--|--|
| development |开发模式，打包更加快速，省了代码优化步骤 |
| production|生产模式，打包比较慢，会开启 tree-shaking 和 压缩代码|
| none|不使用任何默认优化选项 |

## Loader
### 为什么需要 `Loader`
`webpack`  默认支持处理  `JS`  与  `JSON`  文件，其他类型都处理不了，这里必须借助  `Loader`  来对不同类型的文件的进行处理

### Loader 的作用
**`Loader`  就是将  `Webpack`  不认识的内容转化为认识的内容**

### 常用的 `Loader` 及其作用

-  `babel-loader` ：是一个  `JavaScript ` 的编译器，可以将新版本的  `JavaScript`  或者采用  `JavaScript`  语法外延扩展，比如将 `ES6` 代码翻译成 `ES5` 代码
- `file-loader ` :可以指定要复制和放置资源文件的位置，以及如何使用版本哈希命名以获得更好的缓存，并在代码中通过 `URL` 去引用输出的文件
- `url-loader` : 和 `file-loader` 功能相似，但是可以通过指定阈值来根据文件大小使用不同的处理方式（小于阈值则返回 `base64` 格式编码并将文件的  `data-url` 内联到 `bundle` 中）
- `raw-loader` : 加载文件原始内容
- `image-webpack-loader` :  加载并压缩图片资源
- `awesome-typescirpt-loader` : 将 `typescript` 转换为 `javaScript`  并且性能由于 `ts-loader` 
- `sass-loader` : 将 `SCSS/SASS` 代码转换为 `CSS` 
- `css-loader` : 加载 `CSS` 代码 支持模块化、压缩、文件导入等功能特性 
- `style-loader` : 把 `CSS` 代码注入到 `js` 中，通过 `DOM`  操作去加载 `CSS` 代码
- `source-map-loader` : 加载额外的 `Source Map` 文件
- `eslint-loader` : 通过 `ESlint`  检查 `js` 代码
- `cache-loader` : 可以在一些开销较大的 `Loader` 之前添加可以将结果缓存到磁盘中，提高构建的效率
- `thread-loader` : 多线程打包，加快打包速度

### Loader 的本质
`Loader` 本质上是一个函数，负责代码的转译，即对接收到的内容进行转换后将转换后的结果返回 配置 `Loader` 通过在  `modules.rules` 中以数组的形式配置

`Loader` 的执行有以下两个阶段：
- `Pitching`  阶段:  `loader`  上的  `pitch`  方法，按照 `后置(post)、行内(inline)、普通(normal)、前置(pre)`  的顺序调用。
- `Normal` 阶段:  `loader`  上的 常规方法，按照 `前置(pre)、普通(normal)、行内(inline)、后置(post)` 的顺序调用。模块源码的转换， 发生在这个阶段。

### 如何保证众多Loader按照想要的顺序执行

可以通过 `enforce` 来强制控制 `Loader` 的执行顺序 。（ `pre`  表示在所有正常的 `loader` 执行之前执行， `post` 则表示在之后执行）

## Plugin

与  `Loader ` 用于转换特定类型的文件不同，插件（ `Plugin` ）可以贯穿  `Webpack ` 打包的生命周期，执行不同的任务，从而扩展`Webpack` 的功能

### 常用的 Plugin 及其作用

-  `define-plugin` : 定义环境变量
-  `web-webpack-plugin` : 为单页面应用输出 `HTML`  性能优于 `html-webpack-plugin` 
- `clean-webpack-plugin` : 每次打包时删除上次打包的产物, 保证打包目录下的文件都是最新的。
 **tips** : `Webpack 5.20` 以后可以通过 `output.clean` 属性达到同样的效果,所以此插件用处不大了
-  `terser-webpack-plugin` :  内部封装了  `terser`  库，用于处理  `js ` 的压缩和混淆
 **tips :** `webpack v5`  开箱即带有最新版本的  `terser-webpack-plugin` 。如果你使用的是  `webpack v5`  或更高版本，同时希望自定义配置，那么仍需要安装  `terser-webpack-plugin` 。如果使用  `webpack v4` ，则必须安装  `terser-webpack-plugin v4`  的版本。
- `mini-css-extract-plugin` : 将 `CSS` 提取为独立文件，支持按需加载
- `css-minimize-webpack-plugin` : 压缩 `CSS` 代码 
tips:  `css` 文件的压缩需要 `mini-css-extract-plugin` 和 `css-minimize-webpack-plugin`  的配合使用 即先使用 `mini-css-extract-plugin` 将 `css` 代码抽离成单独文件，之后使用  `css-minimize-webpack-plugin` 对 `css` 代码进行压缩
- `compression-webpack-plugin` : 生产环境采用 `gzip` 压缩 `JS` 和 `CSS` 
- `webpack-bundle-analyzer` : 可视化 `webpack` 输出文件大小的根据
- `speed-measure-webpack-plugin` : 用于分析各个 `loader` 和 `plugin` 的耗时，**可用于性能分析**
- `webpack-dashboard` : 可以更友好地展示打包相关信息

### Plugin 的本质
`Plugin` 本质上是一个带有 `apply(compiler)` 的函数，基于 `tapable` 这个事件流框架来监听 `webpack` 构建/打包过程中发布的 `hooks` 来通过自定义的逻辑和功能来改变输出结果。  `Plugin` 通过 `plugins`  以数组的形式配置

### 与 Loader 的区别
 `Loade` r主要负责将代码转译为 `webpack`  可以处理的 `JavaScript` 代码，而  `Plugin` 更多的是负责通过接入 `webpack` 构建过程来影响构建过程以及产物的输出。 

`Loader` 的职责相对比较单一简单，而 `Plugin` 更为丰富多样。

## devServer
可以通过 `webpack-dev-server` 启动本地服务

### 安装 webpack-dev-server
```bash
npm intall webpack-dev-server -D
```

### 配置 devServer

```javascript
const path = require('path');

module.exports = {
  //...
  devServer: {
    static: {
      directory: path.join(__dirname, 'public'),
    },
    compress: true,
    port: 9000,
  },
};
```
**tips : 为什么要配置 `static.directory`?**

因为  `webpack`  在进行打包的时候，对静态文件的处理，例如图片，都是直接  `copy`  到  `dist`  目录下面。但是对于本地开发来说，这个过程太费时，也没有必要，所以在设置 `static.directory` 之后，就直接到对应的静态目录下面去读取文件，而不需对文件做任何移动，节省了时间和性能开销。

 `devServer` 详细配置请参考 [官网](https://webpack.docschina.org/configuration/dev-server/#devserver)

## devtool ( `sourceMap` )

`SourceMap`  是一种映射关系，当项目运行后，如果出现错误，我们可以利用  `SourceMap`  反向定位到源码位置。在 `webpack` 中通过 `devtool` 字段来配置 `sourceMap` 策略。

### 配置 devtool 

```javascript
const config = {
  entry: './src/index.js', // 打包入口地址
  output: {
    filename: 'bundle.js', // 输出文件名
    path: path.join(__dirname, 'dist'), // 输出文件目录
  },
  devtool: 'source-map',
  module: { 
     // ...
  }
  // ...
```

### sourcemap 取值比较
|devtool|build  |rebuild|显示代码	|SourceMap 文件	|
|--|--|--|--|--|
| (none) |  很快|很快|无|无|
| eval|  快|很快（cache）|编译后|无|
| source-map|  很慢	|很慢|源代码|有|
| eval-source-map|  很慢	|一般（cache）|编译后|有（dataUrl）|
| eval-cheap-source-map|  一般|快（cache）|编译后|有（dataUrl）|
| eval-cheap-module-source-map|  慢|快（cache）|源代码|有（dataUrl）|
| inline-source-map|  很慢|很慢|源代码|有（dataUrl）|
| hidden-source-map|  很慢|很慢|源代码|有|
| nosource-source-map|  很慢|很慢|源代码|无）|

### 推荐配置
#### 官方推荐
##### 开发环境
以下选项非常适合开发环境：

- **`eval`**  - 每个模块都使用 `eval()`  执行，并且都有  `//# sourceURL` 。此选项会非常快地构建。**主要缺点是**，由于会映射到转换后的代码，而不是映射到原始代码（没有从  `loader`  中获取  `source map` ），所以**不能正确的显示行数**。
- **`eval-source-map`** - 每个模块使用 `eval()`  执行，并且  `source map`  转换为 `DataUrl ` 后添加到 `eval()` 中。初始化 `source map`  时比较慢，但是会在重新构建时提供比较快的速度，并且生成实际的文件。行数能够正确映射，因为会映射到原始代码中。它会生成用于开发环境的最佳品质的` source map`。 
- **`eval-cheap-source-map`**  - 类似` eval-source-map` ，每个模块使用` eval()`  执行。这是 `"cheap(低开销)"` 的 `source map` ，因为它没有生成列映射(`column mapping`)，只是映射行数。它会忽略源自` loader` 的 `source map`，并且仅显示转译后的代码，就像 `eval devtool`。
- **`eval-cheap-module-source-map`** - 类似 `eval-cheap-source-map`，并且，在这种情况下，源自 `loader` 的 `source map` 会得到更好的处理结果。然而，`loader source map` 会被简化为每行一个映射(`mapping`)。

##### 生产环境
这些选项通常用于生产环境中：

- **`(none)`**（省略 `devtool `选项） - 不生成 `source map`。这是一个不错的选择。
- **`source-map`** - 整个 `source map` 作为一个单独的文件生成。它为 `bundle` 添加了一个引用注释，以便开发工具知道在哪里可以找到它。
- `hidden-source-map` - 与 `source-map` 相同，但不会为` bundle` 添加引用注释。如果你只想 `source map` 映射那些源自错误报告的错误堆栈跟踪信息，但不想为浏览器开发工具暴露你的 `source map`，这个选项会很有用。
- `nosources-source-map` - 创建的 `source map` 不包含 `sourcesContent`(源代码内容)。它可以用来映射客户端上的堆栈跟踪，而无须暴露所有的源代码。你可以将 `source map` 文件部署到 `web` 服务器。

#### 个人推荐
##### 本地开发
推荐：`eval-cheap-module-source-map`
理由：

- 本地开发首次打包慢点没关系，因为 `eval `缓存的原因，`rebuild` 会很快
- 开发中，我们每行代码不会写的太长，只需要定位到行就行，所以加上 `cheap`
- 我们希望能够找到源代码的错误，而不是打包后的，所以需要加上` module`

##### 生产环境
推荐：`(none)`
理由: 不生成 `source map`。这是一个不错的选择

## resolve
配置模块如何解析。webpack 提供合理的默认值，但是还是可能会修改一些解析的细节。

```javascript
module.exports = {
  resolve: {
    modules: [path.resolve(__dirname,'src'), 'node_modules'],
    extensions: ['.js', '.jsx', '.react.js', '.css', '.json'],
    // import导入时别名，减少耗时的递归解析操作
    alias: {
      '@components': path.resolve(`${project}/components`),
      '@style': path.resolve('asset/style'),
    },
  },
}
```

### modules

- 设置模块导入规则，`import/require` 时会直接在这些目录找文件
- 可以指明存放第三方模块的绝对路径，以减少寻找
- 默认值为 `node_modules`
- 告诉 `webpack` 优先 `src`  目录下查找需要解析的文件，会大大节省查找时间
### extensions
-  配置 `extensions`后, `import` 导入时可省略后缀
- 注意：尽可能的减少后缀尝试的可能性,故高频文件后缀名放前面
- 手动配置后，默认配置会被覆盖
- 如果想保留默认配置，可以用 ... 扩展运算符代表默认配置
如：

```javascript
const config = {
  //...
  resolve: {
    extensions: ['.ts', '...'], 
  },
};
```
### alias
`alias` 用的创建 `import` 或 `require` 的别名，用来简化模块引用，项目中基本都需要进行配置

## optimization
从 `webpack 4` 开始，会根据你选择的 `mode` 来执行不同的优化， 不过所有的优化还是可以手动配置和重写。

主要涉及两方面的优化:
- 最小化包
- 拆包

### 最小化包

#### removeAvailableModules 
如果模块已经包含在所有父级模块中，告知 `webpack` 从 `chunk` 中检测出这些模块，或移除这些模块。将 `optimization.removeAvailableModules` 设置为 `true` 以启用这项优化。在  `production` 模式 中默认会被开启。

```javascript
module.exports = {
  //...
  optimization: {
    removeAvailableModules: true,
  },
};
```

#### removeEmptyChunks
如果 `chunk` 为空，告知` webpack`  检测或移除这些 `chunk`,（简单来说就是删除空模块）。默认值为`true`，将 `optimization.removeEmptyChunks` 设置为 `false `可以禁用这项优化。

```javascript
module.exports = {
  //...
  optimization: {
    removeEmptyChunks: false,
  },
};
```

#### providedExports
告知  `webpack`  去确定那些由模块提供的导出内容，为 `export * from ... ` 生成更多高效的代码。默认 `optimization.providedExports` 会被启用。

```javascript
module.exports = {
  //...
  optimization: {
    providedExports: true,
  },
};
```

#### usedExports 
告知 ` webpack`  去决定每个模块使用的导出内容。这取决于  `optimization.providedExports`  选项。由  `optimization.usedExports`  收集的信息会被其它优化手段或者代码生成使用，比如未使用的导出内容不会被生成，当所有的使用都适配，导出名称会被处理做单个标记字符。 **在压缩工具中的无用代码清除会受益于该选项，而且能够去除未使用的导出内容。** 默认值为 `true`

#### sideEffects
告知  `webpack ` 去辨识  `package.json`  中的 副作用 标记或规则，以跳过那些当导出不被使用且被标记不包含副作用的模块。

 `optimization.sideEffects`  取决于  `optimization.providedExports`  被设置成启用。这个依赖会有构建时间的损耗，但去掉模块会对性能有正面的影响，因为更少的代码被生成。该优化的效果取决于你的代码库， 可以尝试这个特性以获取一些可能的性能优化。
#### splitChunks
提取公共包 。
 `webpack`  将根据以下条件自动拆分  `chunks` ：
 
 - 新的 `chunk` 可以被共享，或者模块来自于 `node_modules` 文件夹
 - 新的  `chunk`  体积大于 `20kb`（在进行 `min+gz` 之前的体积）
 - 当按需加载 `chunks` 时，并行请求的最大数量小于或等于 30
 - 当加载初始化页面时，并发请求的最大数量小于或等于 30

当尝试满足最后两个条件时，最好使用较大的  `chunks` 。

下面这个配置对象代表 `SplitChunksPlugin` 的默认行为。

```javascript
module.exports = {
  //...
  optimization: {
    splitChunks: {
      chunks: 'async',
      minSize: 20000,
      minRemainingSize: 0,
      minChunks: 1,
      maxAsyncRequests: 30,
      maxInitialRequests: 30,
      enforceSizeThreshold: 50000,
      cacheGroups: {
        defaultVendors: {
          test: /[\\/]node_modules[\\/]/,
          priority: -10,
          reuseExistingChunk: true,
        },
        default: {
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true,
        },
      },
    },
  },
};
```

关于 `splitChunks` 的详细配置请参考 [官网](https://webpack.docschina.org/plugins/split-chunks-plugin/)

#### minimizer
允许你通过提供一个或多个定制过的  `TerserPlugin` 实例，覆盖默认压缩工具(`minimizer`)
配合 `TerserPlugin` 实现代码压缩和变量混淆,以及生产环境去除 `console` 和 `debugger` 等

```javascript
const TerserPlugin = require('terser-webpack-plugin');

module.exports = {
  optimization: {
    minimizer: [
      new TerserPlugin({
        parallel: true,
        terserOptions: {
          // https://github.com/webpack-contrib/terser-webpack-plugin#terseroptions
        },
      }),
    ],
  },
};
```

在 `optimization.minimizer` 中可以使用` '...' `来访问默认值。

```javascript
module.exports = {
  optimization: {
    minimizer: [new CssMinimizer(), '...'],
  },
};
```

### 拆包
当包过大时，如果我们更新一小部分的包内容，那么整个包都需要重新加载，如果我们把这个包拆分，那么我们仅仅需要重新加载发生内容变更的包，而不是所有包，有效的利用了缓存。

#### 拆分 node_modules
很多情况下，我们不需要手动拆分包，可以使用  `optimization.splitChunks` ：

```javascript
const path = require('path');
module.exports = {
  entry: path.resolve(__dirname, 'src/index.js'),
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[contenthash].js',
  },
  optimization: {
    splitChunks: {
      // 对所有的包进行拆分
      chunks: 'all',
    },
  },
};

```

我们不必制定拆包策略， `chunks: all ` 会自动将  `node_modules`  中的所有内容放入一个名为  `vendors〜main.js`  的文件中。

#### 拆分业务代码

```javascript
module.exports = {
  entry: {
    main: path.resolve(__dirname, 'src/index.js'),
    ProductList: path.resolve(__dirname, 'src/ProductList/ProductList.js'),
    ProductPage: path.resolve(__dirname, 'src/ProductPage/ProductPage.js'),
    Icon: path.resolve(__dirname, 'src/Icon/Icon.js'),
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[contenthash:8].js',
  },
};

```

采用多入口的方式，当有业务代码更新时，更新相应的包即可

#### 拆分第三方库

```javascript
const path = require('path');
const webpack = require('webpack');

module.exports = {
  entry: path.resolve(__dirname, 'src/index.js'),
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[contenthash].js',
  },
  optimization: {
    runtimeChunk: 'single',
    splitChunks: {
      chunks: 'all',
      maxInitialRequests: Infinity,
      minSize: 0,
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name(module) {
            // 获取第三方包名
            const packageName = module.context.match(/[\\/]node_modules[\\/](.*?)([\\/]|$)/)[1];

            // npm 软件包名称是 URL 安全的，但是某些服务器不喜欢@符号
            return `npm.${packageName.replace('@', '')}`;
          },
        },
      },
    },
  },
};
```
当第三方包更新时，仅更新相应的包即可。
注意，当包太多时，浏览器会发起更多的请求，并且当文件过小时，对代码压缩也有影响。

## performance
当打包时出现超过特定文件限制的资产和入口点， `performance` 配置如何显示性能提示

```javascript
module.exports = {
  performance: {
    // 可选 warning、error、false
    // false：性能设置,文件打包过大时，不报错和警告，只做提示
    // warning：显示警告，建议用在开发环境
    // error：显示错误，建议用在生产环境，防止部署太大的生产包，从而影响网页性能
    hints: false
  }
}
```

## watch
监视文件更新，并在文件更新时重新编译：

```javascript
module.export = {
  // 启用监听模式
  watch: true,
}
```

在 `webpack-dev-server` 和 `webpack-dev-middleware` 中，默认启用了监视模式。

或者我们可以在命令行里启动监听（ `--watch` ）：

```bash
webpack --watch --config webpack.dev.config.js
```

## watchOptions

```javascript
module.export = {
  watch: true,
  // 自定义监视模式
  watchOptions: {
    // 排除监听
    ignored: /node_modules/,
    // 监听到变化发生后，延迟 300ms（默认） 再去执行动作，
    // 防止文件更新太快导致重新编译频率太高
    aggregateTimeout: 300,
    // 判断文件是否发生变化是通过不停的去询问系统指定文件有没有变化实现的
    // 默认 1000ms 询问一次
    poll: 1000
  }
}
```

## externals
排除打包时的依赖项，不纳入打包范围内，例如你项目中使用了 `jquery` ，并且你在 `html` 中引入了它，那么在打包时就不需要再把它打包进去：

```html
<script
  src="https://code.jquery.com/jquery-3.1.0.js"
  integrity="sha256-slogkvB1K3VOkzAI8QITxV3VzpOnkeNVsKvtkYLMjfk="
  crossorigin="anonymous">
</script>
```

配置：

```javascript
module.exports = {
  // 打包时排除 jquery 模块
  externals: {
    jquery: 'jQuery'
  }
};
```

## target
构建目标，用于为 ` webpack`  指定一个环境：

```javascript
module.exports = {
  // 编译为类浏览器环境里可用（默认）
  target: 'web'
};
```

## cache
缓存生成的  `webpack`  模块和块以提高构建速度。在开发模式中，缓存设置为  `type: 'memory' ` ，在生产模式中禁用。 `cache: true`  是  `cache: {type: 'memory'}`  的别名。要禁用缓存传递  `false`  ：

```javascript
module.exports = {
  cache: true
}
```

在内存中，缓存仅在监视模式下有用，并且我们假设你在开发中使用监视模式。 在不进行缓存的情况下，内存占用空间较小。

## name

```javascript
module.exports = {
  name: 'react-app'
};
```


