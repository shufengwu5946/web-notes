# 概念

本质上，webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。

示意图：

![](./imgs/webpack.png)

关键概念：

* 入口(entry)
* 输出(output)
* loader
* 插件(plugins)



# 安装

在开始之前，请确保安装了 **Node.js** 的最**新版本**。

全局安装方式不推荐。

推荐**本地安装**方式：

要安装最新版本或特定版本，请运行以下命令之一：

安装最新版本

```bash
npm install --save-dev webpack
```

安装指定版本

```bash
npm install --save-dev webpack@<version>
```

如果你使用 webpack 4+ 版本，你还需要安装 CLI。

```bash
npm install --save-dev webpack-cli
```

## Getting started

创建项目目录。

执行如下命令:

```bash
// -y就是yes的意思，在init的时候省去了敲回车的步骤，生成的默认的package.json
npm init -y
npm install webpack webpack-cli --save-dev
```
## 实现最简项目

项目**目录结构**：

```
wp
|- package.json
|- webpack.config.js
|- /dist
  |- main.js
  |- index.html
|- /src
  |- index.js
|- /node_modules
```
**webpack.config.js:**

```
const path = require('path');

module.exports = {
    entry: './src/index.js',
    output: {
        filename: 'main.js',
        path: path.resolve(__dirname, 'dist')
    }
};
```
**dist/index.html:**

```html
<!doctype html>
<html>

<head>
    <title>最简项目</title>
</head>

<body>
    <script src="main.js"></script>
</body>

</html>
```
**src/index.js**:

```
function component() {
  var element = document.createElement('div');
  element.innerHTML = 'webpack test';
  return element;
}

document.body.appendChild(component());
```
**package.json增加:**
```
{
  ...
    
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
+   "build": "webpack"
  },
    
  ...
}
```
执行 npm run build 命令，在dist目录下生成main.js文件。



# 管理资源

## 加载CSS

安装并添加 style-loader 和 css-loader：
```
npm install --save-dev style-loader css-loader
```
webpack.config.js中添加
```
  const path = require('path');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    },
+   module: {
+     rules: [
+       {
+         test: /\.css$/,
+         use: [
+           'style-loader',
+           'css-loader'
+         ]
+       }
+     ]
+   }
  };
```
> webpack 根据正则表达式，来确定应该查找哪些文件，并将其提供给指定的 loader。在这种情况下，以 .css 结尾的全部文件，都将被提供给 style-loader 和 css-loader。

> 这使你可以在依赖于此样式的文件中 import './style.css'。现在，当该模块运行时，含有 CSS 字符串的 <style> 标签，将被插入到 html 文件的 <head> 中。

> css-loader: 解释(interpret) @import 和 url() ，会 import/require() 后再解析(resolve)它们。

> style-loader: Adds CSS to the DOM by injecting a <style> tag

举例：

src/style.css

```
.content {
    color:green;
}
```
src/index.js

```
import './style.css'
function component() {
  var element = document.createElement('div');
  element.innerHTML = 'webpack test';
  element.classList.add('content');
  return element;
}

document.body.appendChild(component());
```

### css提取？？？

### postcss ？？？

### less ？？？

### sass ？？？

## 加载图片

使用 file-loader，我们可以轻松地将这些内容混合到 CSS 中：
```
npm install --save-dev file-loader
```
webpack.config.js文件增加
```
  const path = require('path');

  module.exports = {
    
    ...
    
    module: {
      rules: [
      
        ...
        
+       {
+         test: /\.(png|svg|jpg|gif)$/,
+         use: [
+           'file-loader'
+         ]
+       }
      ]
    }
  };
```

举例：
src/index.js
```
  import _ from 'lodash';
  import './style.css';
+ import Icon from './icon.png';

  function component() {
    var element = document.createElement('div');
    ...

+   // 将图像添加到我们现有的 div。
+   var myIcon = new Image();
+   myIcon.src = Icon;
+
+   element.appendChild(myIcon);

    return element;
  }

  document.body.appendChild(component());
```
或

src/style.css
```
  .hello {
    color: red;
+   background: url('./icon.png');
  }
```
### image-webpack-loader？？？

### url-loader？？？

### html-loader？？？

## 加载字体

那么，像字体这样的其他资源如何处理呢？file-loader 和 url-loader 可以接收并加载任何文件，然后将其输出到构建目录。这就是说，我们可以将它们用于任何类型的文件，包括字体。

webpack.config.js：
```
  const path = require('path');

  module.exports = {
    ...
    module: {
      rules: [
        ...
        
+       {
+         test: /\.(woff|woff2|eot|ttf|otf)$/,
+         use: [
+           'file-loader'
+         ]
+       }
      ]
    }
  };
```

举例：

src/style.css
```
+ @font-face {
+   font-family: 'MyFont';
+   src:  url('./my-font.woff2') format('woff2'),
+         url('./my-font.woff') format('woff');
+   font-weight: 600;
+   font-style: normal;
+ }

  .hello {
    color: red;
+   font-family: 'MyFont';
    background: url('./icon.png');
  }
```
> https://www.fontsquirrel.com/tools/webfont-generator 可以转换字体为浏览器可以识别的字体。

## 加载数据

* 可以加载的有用资源还有数据，如 JSON 文件，CSV、TSV 和 XML。
* JSON 支持实际上是内置的(例如，直接使用import Data from './data.json' 默认将正常运行）。
* 导入 CSV、TSV 可以使用 csv-loader
* 导入 XML可以使用 xml-loader

安装
```
npm install --save-dev csv-loader xml-loader
```
webpack.config.js
```
  const path = require('path');

  module.exports = {
    
    module: {
      rules: [
        ...
        
+       {
+         test: /\.(csv|tsv)$/,
+         use: [
+           'csv-loader'
+         ]
+       },
+       {
+         test: /\.xml$/,
+         use: [
+           'xml-loader'
+         ]
+       }
      ]
    }
  };  
```

现在，你可以 import 这四种类型的数据(JSON, CSV, TSV, XML)中的任何一种。

## 全局资源

### alias？？？



# 管理输出

到目前为止，我们在 index.html 文件中手动引入所有资源，然而随着应用程序增长，并且一旦开始对文件名使用哈希(hash)]并输出多个 bundle，手动地对 index.html 文件进行管理，一切就会变得困难起来。然而，可以通过一些插件，会使这个过程更容易操控。

## 设定 HtmlWebpackPlugin 

首先安装插件：
```
npm install --save-dev html-webpack-plugin
```
webpack.config.js
```
  const path = require('path');
+ const HtmlWebpackPlugin = require('html-webpack-plugin');

  module.exports = {
    ...
    
+   plugins: [
+     new HtmlWebpackPlugin({
+       title: 'Output Management'
+     })
+   ],
    
    ...
    
  };
```
HtmlWebpackPlugin 还是会默认生成 index.html 文件。这就是说，它会用新生成的 index.html 文件，把我们的原来的替换。

执行npm run build后，如果你在代码编辑器中将 index.html 打开，你就会看到 HtmlWebpackPlugin 创建了一个全新的文件，所有的 bundle 会自动添加到 html 中。

###  html-webpack-template？？？

## 清理 /dist 文件夹 

通常，在每次构建前清理 /dist 文件夹，是比较推荐的做法，因此只会生成用到的文件。

clean-webpack-plugin 是一个比较普及的管理插件，让我们安装和配置下。
```
npm install clean-webpack-plugin --save-dev
```
webpack.config.js
```
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
+ const CleanWebpackPlugin = require('clean-webpack-plugin');

  module.exports = {
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },
    plugins: [
+     new CleanWebpackPlugin(['dist']),
      new HtmlWebpackPlugin({
        title: 'Output Management'
      })
    ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```
现在执行 npm run build，再检查 /dist 文件夹。如果一切顺利，你现在应该不会再看到旧的文件，只有构建后生成的文件！

## manifest？？？

### WebpackManifestPlugin？？？



# 开发环境

## 使用 source map 

当 webpack 打包源代码时，可能会很难追踪到错误和警告在源代码中的原始位置。例如，如果将三个源文件（a.js, b.js 和 c.js）打包到一个 bundle（bundle.js）中，而其中一个源文件包含一个错误，那么堆栈跟踪就会简单地指向到 bundle.js。这并通常没有太多帮助，因为你可能需要准确地知道错误来自于哪个源文件。

为了更容易地追踪错误和警告，JavaScript 提供了 source map 功能，将编译后的代码映射回原始源代码。如果一个错误来自于 b.js，source map 就会明确的告诉你。

source map 有很多不同的选项可用，我们使用 inline-source-map 选项为例作为说明：

在 print.js 文件中生成一个错误：

src/print.js
```
  export default function printMe() {
-   console.log('I get called from print.js!');
+   cosnole.error('I get called from print.js!');
  }
```
npm run build 之后，浏览器控制台定位错误在app.bundle.js文件：
```
app.bundle.js:1 I get called from print.js!
r @ app.bundle.js:1
```
做如下修改：

webpack.config.js
```
const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  const CleanWebpackPlugin = require('clean-webpack-plugin');

  module.exports = {
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },
+   devtool: 'inline-source-map',
    ...
  };
```
执行npm run build，并在浏览器运行，可以定位错误在源文件print.js
```
I get called from print.js!
r @ print.js:2
```
source map帮助定位了问题的确切位置。

## 开发工具

* webpack's Watch Mode
* webpack-dev-server
* webpack-dev-middleware

### 观察模式（Watch Mode）

添加一个用于启动 webpack 的观察模式的 npm script 脚本：

package.json
```
  {
    ...
    
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
+     "watch": "webpack --watch",
      "build": "webpack"
    },
    
    ...
  }
```
命令行中运行 npm run watch，然后就会看到 webpack 是如何编译代码。 然而，你会发现并没有退出命令行。这是因为 script 脚本当前还在观察文件。

修改并保存文件并检查终端窗口。应该可以看到 webpack 自动重新编译修改后的模块！

> 唯一的缺点是，为了看到修改后的实际效果，你需要刷新浏览器。

### webpack-dev-server

webpack-dev-server 为你提供了一个简单的 web 服务器，并且能够实时重新加载(live reloading)。让我们设置以下：
```
npm install --save-dev webpack-dev-server
```
修改配置文件，告诉开发服务器(dev server)，在哪里查找文件：

webpack.config.js
```
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  const CleanWebpackPlugin = require('clean-webpack-plugin');

  module.exports = {
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },
    devtool: 'inline-source-map',
+   devServer: {
+     contentBase: './dist'
+   },

    ...
    
  };
```
以上配置告知 webpack-dev-server，在 localhost:8080 下建立服务，将 dist 目录下的文件，作为可访问文件。

让我们添加一个 script 脚本，可以直接运行开发服务器(dev server)：

package.json
```
  {
    ...
    
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "watch": "webpack --watch",
+     "start": "webpack-dev-server --open",
      "build": "webpack"
    },
    ...
  }
```
现在，我们可以在命令行中运行 npm start，就会看到浏览器自动加载页面。如果现在修改和保存任意源文件，web 服务器就会自动重新加载编译后的代码。

#### publicPath？？？

#### Hot Module Replacement？？？

### 使用 webpack-dev-middleware

webpack-dev-middleware 是一个容器(wrapper)，它可以把 webpack 处理后的文件传递给一个服务器(server)。 webpack-dev-server 在内部使用了它，然而它也可以作为一个单独的 package 来使用，以便根据需求进行更多自定义设置。

首先，安装 `express` 和 `webpack-dev-middleware`：

```bash
npm install --save-dev express webpack-dev-middleware
```

现在，我们需要调整 webpack 配置文件，以确保 middleware(中间件) 功能能够正确启用：

**webpack.config.js**

```diff
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  const CleanWebpackPlugin = require('clean-webpack-plugin');

  module.exports = {
    ...
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist'),
+     publicPath: '/'
    }
  };
```

我们将会在 server 脚本使用 `publicPath`，以确保文件资源能够正确地 serve 在 `http://localhost:3000` 下，稍后我们会指定 port number(端口号)。接下来是设置自定义 `express` server：

**project**

```diff
  webpack-demo
  |- package.json
  |- webpack.config.js
+ |- server.js
  |- /dist
  |- /src
    |- index.js
    |- print.js
  |- /node_modules
```

**server.js**

```javascript
const express = require('express');
const webpack = require('webpack');
const webpackDevMiddleware = require('webpack-dev-middleware');

const app = express();
const config = require('./webpack.config.js');
const compiler = webpack(config);

// 告诉 express 使用 webpack-dev-middleware，
// 以及将 webpack.config.js 配置文件作为基础配置
app.use(webpackDevMiddleware(compiler, {
  publicPath: config.output.publicPath
}));

// 将文件 serve 到 port 3000。
app.listen(3000, function () {
  console.log('Example app listening on port 3000!\n');
});
```

现在，添加一个 npm script，以使我们更方便地运行服务：

**package.json**

```diff
  {
    ...
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "watch": "webpack --watch",
      "start": "webpack-dev-server --open",
+     "server": "node server.js",
      "build": "webpack"
    },
    ...
  }
```

现在，在 terminal(终端) 中执行 `npm run server`，将会有类似如下信息输出：

```bash
Example app listening on port 3000!
...
          Asset       Size  Chunks                    Chunk Names
  app.bundle.js    1.44 MB    0, 1  [emitted]  [big]  app
print.bundle.js    6.57 kB       1  [emitted]         print
     index.html  306 bytes          [emitted]
...
webpack: Compiled successfully.
```

现在，打开浏览器，访问 `http://localhost:3000`。应该看到webpack 应用程序已经运行！



# 热模块替换

## 启用 HMR 

此功能可以很大程度提高生产效率。

webpack.config.js
```diff
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  const CleanWebpackPlugin = require('clean-webpack-plugin');
+ const webpack = require('webpack');

  module.exports = {
    entry: {
-      app: './src/index.js',
-      print: './src/print.js'
+      app: './src/index.js'
    },
    devtool: 'inline-source-map',
    devServer: {
      contentBase: './dist',
+     hot: true
    },
    plugins: [
      new CleanWebpackPlugin(['dist']),
      new HtmlWebpackPlugin({
        title: '模块热替换'
      }),
+     new webpack.HotModuleReplacementPlugin()
    ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```

> 可以通过命令来修改 [webpack-dev-server](https://github.com/webpack/webpack-dev-server) *的配置：*`webpack-dev-server --hotOnly`

现在，修改 `index.js` 文件，以便在 `print.js` 内部发生变更时，告诉 webpack 接受 updated module。

```diff
  import _ from 'lodash';
  import printMe from './print.js';

  function component() {
    ...
  }

  document.body.appendChild(component());
+
+ if (module.hot) {
+   module.hot.accept('./print.js', function() {
+     console.log('Accepting the updated printMe module!');
+     printMe();
+   })
+ }
```

## 通过 Node.js API 

在 Node.js API 中使用 webpack dev server 时，不要将 dev server 选项放在 webpack 配置对象(webpack config object)中。而是，在创建时，将其作为第二个参数传递。例如：

```
new WebpackDevServer(compiler, options)
```

想要启用 HMR，还需要修改 webpack 配置对象，使其包含 HMR 入口起点。`webpack-dev-server` package 中具有一个叫做 `addDevServerEntrypoints` 的方法，你可以通过使用这个方法来实现。这是关于如何使用的一个基本示例：

**dev-server.js**

```javascript
const webpackDevServer = require('webpack-dev-server');
const webpack = require('webpack');

const config = require('./webpack.config.js');
const options = {
  contentBase: './dist',
  hot: true,
  host: 'localhost'
};

webpackDevServer.addDevServerEntrypoints(config, options);
const compiler = webpack(config);
const server = new webpackDevServer(compiler, options);

server.listen(5000, 'localhost', () => {
  console.log('dev server listening on port 5000');
});
```

> 如果你正在使用 [`webpack-dev-middleware`](https://webpack.docschina.org/guides/development#using-webpack-dev-middleware)，可以通过 [`webpack-hot-middleware`](https://github.com/webpack-contrib/webpack-hot-middleware) package，在自定义 dev server 中启用 HMR。

## 问题 

模块热替换可能比较难以掌握。为了说明这一点，我们回到刚才的示例中。如果你继续点击示例页面上的按钮，你会发现控制台仍在打印旧的 `printMe` 函数。

这是因为按钮的 `onclick` 事件处理函数仍然绑定在旧的 `printMe` 函数上。

为了让 HMR 正常工作，我们需要更新代码，使用 `module.hot.accept` 将其绑定到新的 `printMe` 函数上：

**index.js**

```diff
  import _ from 'lodash';
  import printMe from './print.js';

  function component() {
    ...
  }

- document.body.appendChild(component());
+ let element = component(); // 存储 element，以在 print.js 修改时重新渲染
+ document.body.appendChild(element);

  if (module.hot) {
    module.hot.accept('./print.js', function() {
      console.log('Accepting the updated printMe module!');
-     printMe();
+     document.body.removeChild(element);
+     element = component(); // Re-render the "component" to update the click handler
+     element = component(); // 重新渲染 "component"，以便更新 click 事件处理函数
+     document.body.appendChild(element);
    })
  }
```

## HMR 加载样式 

借助于 style-loader，使用模块热替换来加载 CSS 实际上极其简单。此 loader 在幕后使用了 module.hot.accept，在 CSS 依赖模块更新之后，会将其 patch(修补) 到 <style> 标签中。

安装loader ：
```
npm install --save-dev style-loader css-loader
```
然后更新配置文件，使用这两个 loader。

webpack.config.js
```
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  const CleanWebpackPlugin = require('clean-webpack-plugin');
  const webpack = require('webpack');

  module.exports = {
    entry: {
      app: './src/index.js'
    },
    devtool: 'inline-source-map',
    devServer: {
      contentBase: './dist',
      hot: true
    },
+   module: {
+     rules: [
+       {
+         test: /\.css$/,
+         use: ['style-loader', 'css-loader']
+       }
+     ]
+   },
    plugins: [
      new CleanWebpackPlugin(['dist']),
      new HtmlWebpackPlugin({
        title: '模块热替换'
      }),
      new webpack.HotModuleReplacementPlugin()
    ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```

## 其他代码和框架 

### React Hot Loader？？？

社区还提供许多其他 loader 和示例，可以使 HMR 与各种框架和库平滑地进行交互……

* **React Hot Loader：实时调整 react 组件。**
* Vue Loader：此 loader 支持 vue 组件的 HMR，提供开箱即用体验。
* Elm Hot Loader：支持 Elm 编程语言的 HMR。
* Angular HMR：没有必要使用 loader！直接修改 NgModule 主文件就够了，它可以完全控制 HMR API。



# tree shaking

*tree shaking* 是一个术语，通常用于描述移除 JavaScript 上下文中的未引用代码(dead-code)。它依赖于 ES2015 模块语法的 [静态结构](http://exploringjs.com/es6/ch_modules.html#static-module-structure) 特性，例如 [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) 和 [`export`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)。

webpack 2 正式版本内置支持 ES2015 模块（也叫做 *harmony modules*）和未使用模块检测能力。新的 webpack 4 正式版本扩展了此检测能力，通过 `package.json` 的 `"sideEffects"` 属性作为标记，向 compiler 提供提示，表明项目中的哪些文件是 "pure(纯的 ES2015 模块)"，由此可以安全地删除文件中未使用的部分。

## sideEffects

在一个纯粹的 ESM 模块世界中，很容易识别出哪些文件有 side effect。然而，我们的项目无法达到这种纯度，所以，此时有必要提示 webpack compiler 哪些代码是“纯粹部分”。

通过 package.json 的 `"sideEffects"` 属性，来实现这种方式。

```json
{
  "name": "your-project",
  "sideEffects": false
}
```

如果所有代码都不包含 side effect，我们就可以简单地将该属性标记为 `false`，来告知 webpack，它可以安全地删除未用到的 export。

> "side effect(副作用)" 的定义是，在导入时会执行特殊行为的代码，而不是仅仅暴露一个 export 或多个 export。举例说明，例如 polyfill，它影响全局作用域，并且通常不提供 export。

如果你的代码确实有一些副作用，可以改为提供一个数组：

```json
{
  "name": "your-project",
  "sideEffects": [
    "./src/some-side-effectful-file.js"
  ]
}
```

数组方式支持相对路径、绝对路径和 glob 模式匹配相关文件。它在内部使用 [micromatch](https://github.com/micromatch/micromatch#matching-features)。

> 注意，所有导入文件都会受到 tree shaking 的影响。这意味着，如果在项目中使用类似 `css-loader` 并 import 一个 CSS 文件，则需要将其添加到 side effect 列表中，以免在生产模式中无意中将它删除：

```json
{
  "name": "your-project",
  "sideEffects": [
    "./src/some-side-effectful-file.js",
    "*.css"
  ]
}
```

最后，还可以在 [`module.rules` 配置选项](https://webpack.docschina.org/configuration/module/#module-rules) 中设置 `"sideEffects"`。

### micromatch？？？

## 压缩输出结果

通过 `import` 和 `export` 语法，我们已经找出需要删除的“未引用代码(dead code)”，然而，不仅仅是要找出，还要在 bundle 中删除它们。为此，我们需要将 `mode` 配置选项设置为 [`production`](https://webpack.docschina.org/concepts/mode/#mode-production)。

> 注意，也可以在命令行接口中使用 `--optimize-minimize` 标记，来启用 `TerserPlugin`。

准备就绪后，然后运行另一个 npm script `npm run build`，看看输出结果是否发生改变。

> 运行 tree shaking 需要 [ModuleConcatenationPlugin](https://webpack.docschina.org/plugins/module-concatenation-plugin)。通过 `mode: "production"` 可以添加此插件。如果你没有使用 mode 设置，记得手动添加 [ModuleConcatenationPlugin](https://webpack.docschina.org/plugins/module-concatenation-plugin)。

### TerserPlugin？？？

### ModuleConcatenationPlugin ？？？



想要使用 *tree shaking* 必须注意以下……

- 使用 ES2015 模块语法（即 `import` 和 `export`）。
- 确保没有 compiler 将 ES2015 模块语法转换为 CommonJS 模块（这也是流行的 Babel preset 中 @babel/preset-env 的默认行为 - 更多详细信息请查看 [文档](https://babel.docschina.org/docs/en/babel-preset-env#modules)）。
- 在项目 `package.json` 文件中，添加一个 "sideEffects" 属性。
- 通过将 `mode` 选项设置为 [`production`](https://webpack.docschina.org/concepts/mode/#mode-production)，启用 minification(代码压缩) 和 tree shaking。

> 你可以将应用程序想象成一棵树。绿色表示实际用到的 source code(源码) 和 library(库)，是树上活的树叶。灰色表示未引用代码，是秋天树上枯萎的树叶。为了除去死去的树叶，你必须摇动这棵树，使它们落下。

# 生产环境

## 配置

* development(开发环境) 和 production(生产环境) 这两个环境下的构建目标存在着巨大差异。

* 在开发环境中，我们需要：强大的 source map 和一个有着 live reloading(实时重新加载) 或 hot module replacement(热模块替换) 能力的 localhost server。

* 而生产环境目标则转移至其他方面，关注点在于压缩 bundle、更轻量的 source map、资源优化等，通过这些优化方式改善加载时间。

* 由于要遵循逻辑分离，我们通常建议为每个环境编写彼此独立的 webpack 配置。

保留一个 "common(通用)" 配置。为了将这些配置合并在一起，我们将使用一个名为 webpack-merge 的工具。此工具会引用 "common" 配置，因此我们不必再在环境特定(environment-specific)的配置中编写重复代码。

我们先从安装 webpack-merge ：
```bash
npm install --save-dev webpack-merge
```

project

```diff
- |- webpack.config.js
+ |- webpack.common.js
+ |- webpack.dev.js
+ |- webpack.prod.js
```

webpack.common.js

```diff
+ const path = require('path');
+ const HtmlWebpackPlugin = require('html-webpack-plugin');
+ const CleanWebpackPlugin = require('clean-webpack-plugin');
+ 
+ module.exports = {
+     entry: {
+         app: './src/index.js'
+     },
+     output: {
+         filename: '[name].bundle.js',
+         path: path.resolve(__dirname, 'dist')
+     },
+     plugins: [
+         new HtmlWebpackPlugin({
+             title: 'Output Management'
+         }),
+         new CleanWebpackPlugin(['dist'])
+     ]
+ };
```

webpack.dev.js

```diff
+ const merge = require('webpack-merge');
+ const common = require('./webpack.common.js');
+ const webpack = require('webpack');
+ 
+ module.exports = merge(common, {
+     devtool: 'inline-source-map',
+     devServer: {
+         contentBase: './dist',
+         hot: true
+     },
+     plugins: [
+         new webpack.HotModuleReplacementPlugin()
+     ],
+     mode: 'development'
+ });
```

webpack.prod.js

```diff
+ const merge = require('webpack-merge');
+ const common = require('./webpack.common.js');
+
+ module.exports = merge(common,{
+     mode: 'production'
+ });
```

## npm scripts 

让 npm start script 中 webpack-dev-server 使用 development(开发环境) 配置文件，而让 npm run build script 使用 production(生产环境) 配置文件：

package.json
```diff json
  {
    ...
    
    "scripts": {
-     "start": "webpack-dev-server --open",
+     "start": "webpack-dev-server --open --config webpack.dev.js",
-     "build": "webpack"
+     "build": "webpack --config webpack.prod.js"
    },
    
    ...
    
  }
```

## 指定 mode 

许多 library 通过与 process.env.NODE_ENV 环境变量关联，以决定 library 中应该引用哪些内容。例如，当不处于生产环境中时，某些 library 为了使调试变得容易，可能会添加额外的 log(日志记录) 和 test(测试) 功能。并且，在使用 process.env.NODE_ENV === 'production' 时，一些 library 可能针对具体用户的环境，删除或添加一些重要代码，以进行代码执行方面的优化。

从 webpack v4 开始, 指定 mode 会自动地配置 DefinePlugin：

webpack.prod.js
```
  const merge = require('webpack-merge');
  const common = require('./webpack.common.js');

  module.exports = merge(common, {
    mode: 'production',
  });
```

技术上讲，NODE_ENV 是一个由 Node.js 暴露给执行脚本的系统环境变量。通常用于决定在开发环境与生产环境(dev-vs-prod)下，server tools(服务期工具)、build scripts(构建脚本) 和 client-side libraries(客户端库) 的行为。

> 然而，与预期相反，无法在构建脚本 webpack.config.js 中，将 process.env.NODE_ENV 设置为 "production"，请查看 #2537。因此，在 webpack 配置文件中，process.env.NODE_ENV === 'production' ? '[name].[hash].bundle.js' : '[name].bundle.js' 这样的条件语句，无法按照预期运行。

> 任何位于 /src 的本地代码都可以关联到 process.env.NODE_ENV 环境变量，所以以下检查也是有效的：

src/index.js
```diff
  import { cube } from './math.js';
+
+ if (process.env.NODE_ENV !== 'production') {
+   console.log('Looks like we are in development mode!');
+ }

  function component() {
    var element = document.createElement('pre');

    element.innerHTML = [
      'Hello webpack!',
      '5 cubed is equal to ' + cube(5)
    ].join('\n\n');

    return element;
  }

  document.body.appendChild(component());
```

## minification(压缩) 

设置 production mode 配置后，webpack v4+ 会默认压缩你的代码。

> 注意，虽然生产环境下默认使用 TerserPlugin ，并且也是代码压缩方面比较好的选择，但是还有一些其他可选择项。以下有几个同样很受欢迎的插件：
> * BabelMinifyWebpackPlugin
> * ClosureCompilerPlugin
> 如果决定尝试一些其他压缩插件，只要确保新插件也会按照 tree shake 指南中所陈述的具有删除未引用代码(dead code)的能力，以及提供 optimization.minimizer。

### optimization.minimizer？？？

## source mapping(源码映射)

我们鼓励你在生产环境中启用 source map，因为它们对 debug(调试源码) 和运行 benchmark tests(基准测试) 很有帮助。虽然有着如此强大的功能，然而还是应该针对生产环境用途，选择一个可以快速构建的推荐配置（更多选项请查看 devtool）。对于本指南，我们将在生产环境中使用 source-map 选项，而不是我们在开发环境中用到的 inline-source-map：

webpack.prod.js

```diff
  const merge = require('webpack-merge');
  const common = require('./webpack.common.js');

  module.exports = merge(common, {
    mode: 'production',
+   devtool: 'source-map'
  });
```

> 避免在生产中使用 inline-*** 和 eval-***，因为它们会增加 bundle 体积大小，并降低整体性能。

### benchmark tests(基准测试)？？？

### devtool？？？

## 最小化 CSS ？？？

将生产环境下的 CSS 进行压缩会非常重要，请查看 [在生产环境下压缩](https://webpack.docschina.org/plugins/mini-css-extract-plugin/#minimizing-for-production) 章节。

## CLI 替代选项 

* --optimize-minimize 标记将在幕后引用 TerserPlugin。
* 和以上描述的 DefinePlugin 实例相同，--define process.env.NODE_ENV="'production'" 也会做同样的事情。
* webpack -p 将自动地配置上述这两个标记，从而调用需要引入的插件。

> 通常我们建议只使用配置方式，因为在这两种方式中，配置方式能够更准确地理解现在正在做的事情。配置方式还为可以让你更加细微地控制这两个插件中的其他选项。

# 代码分离

代码分离是 webpack 中最引人注目的特性之一。此特性能够把代码分离到不同的 bundle 中，然后可以按需加载或并行加载这些文件。代码分离可以用于获取更小的 bundle，以及控制资源加载优先级，如果使用合理，会极大影响加载时间。

常用的代码分离方法有三种：

* 入口起点：使用 entry 配置手动地分离代码。
* 防止重复：使用 SplitChunksPlugin 去重和分离 chunk。
* 动态导入：通过模块中的内联函数调用来分离代码。

## 入口起点(entry points) 

正如前面提到的，这种方式存在一些隐患：

* 如果入口 chunk 之间包含一些重复的模块，那些重复模块都会被引入到各个 bundle 中。
* 这种方法不够灵活，并且不能动态地将核心应用程序逻辑中的代码拆分出来。

这两点中的第一点，对我们的示例来说毫无疑问是个严重问题，因为我们在 ./src/index.js 中也引入过 lodash，这样就造成在两个 bundle 中重复引用。我们可以通过使用 SplitChunksPlugin 插件来移除重复模块。

## 防止重复(prevent duplication) 

SplitChunksPlugin 插件可以将公共的依赖模块提取到已有的 entry chunk 中，或者提取到一个新生成的 chunk。让我们使用这个插件，将前面示例中重复的 lodash 模块去除：

CommonsChunkPlugin 已经从 webpack v4（代号 legato）中移除。想要了解最新版本是如何处理 chunk，请查看 SplitChunksPlugin。

webpack.config.js
```diff
  const path = require('path');

  module.exports = {
    mode: 'development',
    entry: {
      index: './src/index.js',
      another: './src/another-module.js'
    },
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    },
+   optimization: {
+     splitChunks: {
+       chunks: 'all'
+     }
+   }
  };
```

以下是由社区提供，一些对于代码分离很有帮助的 plugin 和 loader：
> * mini-css-extract-plugin：用于将 CSS 从主应用程序中分离。
> * bundle-loader：用于分离代码和延迟加载生成的 bundle。
> * promise-loader：类似于 bundle-loader ，但是使用了 promise API。

### mini-css-extract-plugin？？？

### bundle-loader？？？

### promise-loader？？？

## 动态导入

当涉及到动态代码拆分时，webpack 提供了两个类似的技术。

* 第一种，也是推荐选择的方式是，使用符合 [ECMAScript 提案](https://github.com/tc39/proposal-dynamic-import) 的 [`import()` 语法](https://webpack.docschina.org/api/module-methods#import-) 来实现动态导入。

* 第二种，则是 webpack 的遗留功能，使用 webpack 特定的 [`require.ensure`](https://webpack.docschina.org/api/module-methods#require-ensure)

### import动态加载

> `import()` *调用会在内部用到* [promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)*。如果在旧版本浏览器中使用* `import()`*，记得使用一个 polyfill 库（例如* [es6-promise](https://github.com/stefanpenner/es6-promise) *或* [promise-polyfill](https://github.com/taylorhakes/promise-polyfill)*），来 shim* `Promise`*。*

**webpack.config.js**

```diff
  const path = require('path');

  module.exports = {
    mode: 'development',
    entry: {
      index: './src/index.js'
    },
    output: {
      filename: '[name].bundle.js',
+     chunkFilename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```

> 注意，这里使用了 `chunkFilename`，它决定 non-entry chunk(非入口 chunk) 的名称。

现在，我们不再使用 statically import(静态导入) `lodash`，而是通过 dynamic import(动态导入) 来分离出一个 chunk：

**src/index.js**

```javascript
function getComponent() {
  return import(/* webpackChunkName: "lodash" */ 'lodash')
    .then(({ default: _ }) => {
      var element = document.createElement('div');
      element.innerHTML = _.join(['Hello', 'webpack'], ' ');
      return element;
    }).catch(error => 'An error occurred while loading the component');
  }

getComponent().then(component => {
  document.body.appendChild(component);
})
```



注意，在注释中我们提供了 `webpackChunkName`。这样会将拆分出来的 bundle 命名为 `lodash.bundle.js`，而不是 `[id].bundle.js`。想了解更多关于 `webpackChunkName` 和其他可用选项，请查看 [`import()`](https://webpack.docschina.org/api/module-methods/#import-) 文档。让我们执行 webpack，看到 `lodash` 分离出一个单独的 bundle：

```bash
...
                   Asset      Size          Chunks             Chunk Names
         index.bundle.js  7.88 KiB           index  [emitted]  index
vendors~lodash.bundle.js   547 KiB  vendors~lodash  [emitted]  vendors~lodash
Entrypoint index = index.bundle.js
...
```

由于 `import()` 会返回一个 promise，因此它可以和 [`async` 函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)一起使用。但是，需要使用像 Babel 这样的预处理器和 [Syntax Dynamic Import Babel Plugin](https://babel.docschina.org/docs/en/babel-plugin-syntax-dynamic-import/#installation)。下面是如何通过 async 函数简化代码：

#### Syntax Dynamic Import Babel Plugin？？？

**src/index.js**

```diff
- function getComponent() {
+ async function getComponent() {
-   return import(/* webpackChunkName: "lodash" */ 'lodash').then({ default: _ } => {
-     var element = document.createElement('div');
-
-     element.innerHTML = _.join(['Hello', 'webpack'], ' ');
-
-     return element;
-
-   }).catch(error => 'An error occurred while loading the component');
+   var element = document.createElement('div');
+   const { default: _ } = await import(/* webpackChunkName: "lodash" */ 'lodash');
+
+   element.innerHTML = _.join(['Hello', 'webpack'], ' ');
+
+   return element;
  }

  getComponent().then(component => {
    document.body.appendChild(component);
  });
```

### require.ensure？？？

## 预取/预加载模块(prefetch/preload module)？？？

## bundle 分析(bundle analysis) 
如果我们以分离代码作为开始，那么就应该以检查模块的输出结果作为结束，对其进行分析是很有用处的。[官方提供分析工具](https://github.com/webpack/analyse) 是一个好的初始选择。下面是一些可选择的社区支持(community-supported)工具：

* webpack-chart：webpack stats 可交互饼图。
* webpack-visualizer：可视化并分析你的 bundle，检查哪些模块占用空间，哪些可能是重复使用的。
* webpack-bundle-analyzer：一个 plugin 和 CLI 工具，它将 bundle 内容展示为便捷的、交互式、可缩放的树状图形式。
* webpack bundle optimize helper：此工具会分析你的 bundle，并为你提供可操作的改进措施建议，以减少 bundle 体积大小。

### 官方提供分析工具？？？

### webpack-chart？？？

### webpack-visualizer？？？

### webpack-bundle-analyzer？？？

### webpack bundle optimize helper？？？



# 懒加载

懒加载或者按需加载，是一种很好的优化网页或应用的方式。这种方式实际上是先把你的代码在一些逻辑断点处分离开，然后在一些代码块中完成某些操作后，立即引用或即将引用另外一些新的代码块。这样加快了应用的初始加载速度，减轻了它的总体体积，因为某些代码块可能永远不会被加载。

## 示例 

我们在[代码分离](https://webpack.docschina.org/guides/code-splitting#dynamic-imports)中的例子基础上，进一步做些调整来说明这个概念。那里的代码确实会在脚本运行的时候产生一个分离的代码块 `lodash.bundle.js` ，在技术概念上“懒加载”它。问题是加载这个包并不需要用户的交互 -- 意思是每次加载页面的时候都会请求它。这样做并没有对我们有很多帮助，还会对性能产生负面影响。

我们试试不同的做法。我们增加一个交互，当用户点击按钮的时候用 console 打印一些文字。但是会等到第一次交互的时候再加载那个代码块（`print.js`）。为此，我们返回到代码分离的例子中，把 `lodash` 放到主代码块中，重新运行*代码分离*中的代码 [final *Dynamic Imports* example](https://webpack.docschina.org/guides/code-splitting#dynamic-imports)。

**project**

```diff
webpack-demo
|- package.json
|- webpack.config.js
|- /dist
|- /src
  |- index.js
+ |- print.js
|- /node_modules
```

**src/print.js**

```js
console.log('The print.js module has loaded! See the network tab in dev tools...');

export default () => {
  console.log('Button Clicked: Here\'s "some text"!');
};
```

**src/index.js**

```diff
+ import _ from 'lodash';
+
- async function getComponent() {
+ function component() {
    var element = document.createElement('div');
-   const _ = await import(/* webpackChunkName: "lodash" */ 'lodash');
+   var button = document.createElement('button');
+   var br = document.createElement('br');

+   button.innerHTML = 'Click me and look at the console!';
    element.innerHTML = _.join(['Hello', 'webpack'], ' ');
+   element.appendChild(br);
+   element.appendChild(button);
+
+   // Note that because a network request is involved, some indication
+   // of loading would need to be shown in a production-level site/app.
+   button.onclick = e => import(/* webpackChunkName: "print" */ './print').then(module => {
+     var print = module.default;
+
+     print();
+   });

    return element;
  }

- getComponent().then(component => {
-   document.body.appendChild(component);
- });
+ document.body.appendChild(component());
```

> 注意当调用 ES6 模块的 `import()` 方法（引入模块）时，必须指向模块的 `.default` 值，因为它才是 promise 被处理后返回的实际的 `module` 对象。

## 框架 

许多框架和类库对于如何用它们自己的方式来实现（懒加载）都有自己的建议。这里有一些例子：

- React: [Code Splitting and Lazy Loading](https://reacttraining.com/react-router/web/guides/code-splitting)
- Vue: [Lazy Load in Vue using Webpack's code splitting](https://alexjoverm.github.io/2017/07/16/Lazy-load-in-Vue-using-Webpack-s-code-splitting/)
- AngularJS: [AngularJS + Webpack = lazyLoad](https://medium.com/@var_bin/angularjs-webpack-lazyload-bb7977f390dd) by [@var_bincom](https://twitter.com/var_bincom)

### Code Splitting and Lazy Loading？？？



# 缓存

以上，我们使用 webpack 来打包模块化的应用程序，webpack 会生成一个可部署的 `/dist` 目录，然后把打包后的内容放置在此目录中。只要 `/dist` 目录中的内容部署到 server 上，client（通常是浏览器）就能够访问网站此 server 的网站及其资源。而最后一步获取资源是比较耗费时间的，这就是为什么浏览器使用一种名为 [缓存](https://searchstorage.techtarget.com/definition/cache) 的技术。可以通过命中缓存，以降低网络流量，使网站加载速度更快，然而，如果我们在部署新版本时不更改资源的文件名，浏览器可能会认为它没有被更新，就会使用它的缓存版本。由于缓存的存在，当你需要获取新的代码时，就会显得很棘手。

## 输出文件的文件名

我们可以通过替换 `output.filename` 中的 [substitutions](https://webpack.docschina.org/configuration/output#output-filename) 设置，来定义输出文件的名称。webpack 提供了一种使用称为 **substitution(可替换模板字符串)** 的方式，通过带括号字符串来模板化文件名。其中，`[contenthash]`substitution 将根据资源内容创建出唯一 hash。当资源内容发生变化时，`[contenthash]` 也会发生变化。

```diff
module.exports = {
    ...
    
    output: {
-     filename: 'bundle.js',
+     filename: '[name].[contenthash].js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```

## 提取引导模板

### 提取runtime

正如我们在 [代码分离](https://webpack.docschina.org/guides/code-splitting) 中所学到的，[`SplitChunksPlugin`](https://webpack.docschina.org/plugins/split-chunks-plugin/) 可以用于将模块分离到单独的 bundle 中。webpack 还提供了一个优化功能，可使用 [`optimization.runtimeChunk`](https://webpack.docschina.org/configuration/optimization/#optimization-runtimechunk) 选项将 runtime 代码拆分为一个单独的 chunk。将其设置为 `single` 来为所有 chunk 创建一个 runtime bundle：

**webpack.config.js**

```diff
  module.exports = {
    ...
    
    output: {
      filename: '[name].[contenthash].js',
      path: path.resolve(__dirname, 'dist')
    },
+   optimization: {
+     runtimeChunk: 'single'
+   }
  };
```

### 提取第三方库到vendor chunk中

将第三方库(library)（例如 `lodash` 或 `react`）提取到单独的 `vendor` chunk 文件中，是比较推荐的做法，这是因为，它们很少像本地的源代码那样频繁修改。因此通过实现以上步骤，利用 client 的长效缓存机制，命中缓存来消除请求，并减少向 server 获取资源，同时还能保证 client 代码和 server 代码版本一致。 这可以通过使用 [SplitChunksPlugin 示例 2](https://webpack.docschina.org/plugins/split-chunks-plugin/#split-chunks-example-2) 中演示的 [`SplitChunksPlugin`](https://webpack.docschina.org/plugins/split-chunks-plugin/) 插件的 [`cacheGroups`](https://webpack.docschina.org/plugins/split-chunks-plugin/#splitchunks-cachegroups) 选项来实现。我们在 `optimization.splitChunks` 添加如下 `cacheGroups` 参数并构建：

**webpack.config.js**

```diff
  var path = require('path');
  const CleanWebpackPlugin = require('clean-webpack-plugin');
  const HtmlWebpackPlugin = require('html-webpack-plugin');

  module.exports = {
    ...
    
    optimization: {
-     runtimeChunk: 'single'
+     runtimeChunk: 'single',
+     splitChunks: {
+       cacheGroups: {
+         vendor: {
+           test: /[\\/]node_modules[\\/]/,
+           name: 'vendors',
+           chunks: 'all'
+         }
+       }
+     }
    }
  };
```

## 模块标识符

这是因为每个 [`module.id`](https://webpack.docschina.org/api/module-variables#module-id-commonjs-) 会默认地基于解析顺序(resolve order)进行增量。也就是说，当解析顺序发生变化，ID 也会随之改变。因此，简要概括：

- `main` bundle 会随着自身的新增内容的修改，而发生变化。
- `vendor` bundle 会随着自身的 `module.id` 的变化，而发生变化。
- `manifest` bundle 会因为现在包含一个新模块的引用，而发生变化。

第一个和最后一个都是符合预期的行为 - 而 `vendor` hash 发生变化是我们要修复的。幸运的是，可以使用两个插件来解决这个问题。第一个插件是 `NamedModulesPlugin`，将使用模块的路径，而不是一个数字 identifier。虽然此插件有助于在开发环境下产生更加可读的输出结果，然而其执行时间会有些长。第二个选择是使用 [`HashedModuleIdsPlugin`](https://webpack.docschina.org/plugins/hashed-module-ids-plugin)，推荐用于生产环境构建：

```diff
module.exports = {
    entry: './src/index.js',
    plugins: [
      new CleanWebpackPlugin(['dist']),
      new HtmlWebpackPlugin({
        title: 'Caching'
      }),
+      new webpack.HashedModuleIdsPlugin()
    ],
    ...
    
  };
```



# 创建 library



# shim 预置依赖

在JavaScript的世界里,有两个词经常被提到,shim和polyfill.它们指的都是什么,又有什么区别?
一个shim是一个库,它将一个新的API引入到一个旧的环境中,而且仅靠旧环境中已有的手段实现
一个polyfill就是一个用在浏览器API上的shim.我们通常的做法是先检查当前浏览器是否支持某个API,如果不支持的话就加载对应的polyfill.然后新旧浏览器就都可以使用这个API了.术语polyfill来自于一个家装产品Polyfilla:

https://www.zhihu.com/question/22129715

Polyfilla是一个英国产品,在美国称之为Spackling Paste(译者注:刮墙的,在中国称为腻子).记住这一点就行:把旧的浏览器想象成为一面有了裂缝的墙.这些[polyfills]会帮助我们把这面墙的裂缝抹平,还我们一个更好的光滑的墙壁(浏览器)
Paul Irish发布过一个Polyfills的总结页面“HTML5 Cross Browser Polyfills”.es5-shim是一个shim(而不是polyfill)的例子,它在ECMAScript 3的引擎上实现了ECMAScript 5的新特性,而且在[Node.js](https://link.zhihu.com/?target=https%3A//www.baidu.com/s%3Fwd%3DNode.js%26tn%3D44039180_cpr%26fenlei%3Dmv6quAkxTZn0IZRqIHckPjm4nH00T1YLPhcvmWckm1TznAmdP1ub0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnHTvP1fsrjb4P1TLnjTvnWR3n0)上和在浏览器上有完全相同的表现(译者注:因为它能在[Node.js](https://link.zhihu.com/?target=https%3A//www.baidu.com/s%3Fwd%3DNode.js%26tn%3D44039180_cpr%26fenlei%3Dmv6quAkxTZn0IZRqIHckPjm4nH00T1YLPhcvmWckm1TznAmdP1ub0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnHTvP1fsrjb4P1TLnjTvnWR3n0)上使用,不光浏览器上,所以它不是polyfill).
作者：Musclemen链接：https://www.zhihu.com/question/22129715/answer/347924641来源：知乎著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

## shim 预置全局变量

还记得我们之前用过的 `lodash` 吗？出于演示目的，例如把这个应用程序中的模块依赖，改为一个全局变量依赖。要实现这些，我们需要使用 `ProvidePlugin` 插件。

使用 [`ProvidePlugin`](https://webpack.docschina.org/plugins/provide-plugin) 后，能够在 webpack 编译的每个模块中，通过访问一个变量来获取一个 package。如果 webpack 看到模块中用到这个变量，它将在最终 bundle 中引入给定的 package。让我们先移除 `lodash` 的 `import` 语句，改为通过插件提供它：

**src/index.js**

```diff
- import _ from 'lodash';
```

**webpack.config.js**

```diff
  const path = require('path');
+ const webpack = require('webpack');

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
-   }
+   },
+   plugins: [
+     new webpack.ProvidePlugin({
+       _: 'lodash'
+     })
+   ]
  };
```

我们本质上所做的，就是告诉 webpack……

> 如果你遇到了至少一处用到 `_` 变量的模块实例，那请你将 `lodash` package 引入进来，并将其提供给需要用到它的模块。



还可以使用 `ProvidePlugin` 暴露出某个模块中单个导出，通过配置一个“数组路径”（例如 `[module, child, ...children?]`）实现此功能。所以，我们假想如下，无论 `join` 方法在何处调用，我们都只会获取到 `lodash`中提供的 `join` 方法。

**src/index.js**

```diff
  function component() {
    var element = document.createElement('div');

-   element.innerHTML = _.join(['Hello', 'webpack'], ' ');
+   element.innerHTML = join(['Hello', 'webpack'], ' ');

    return element;
  }

  document.body.appendChild(component());
```

**webpack.config.js**

```diff
  const path = require('path');
  const webpack = require('webpack');

  module.exports = {
    ...
    
    plugins: [
      new webpack.ProvidePlugin({
-       _: 'lodash'
+       join: ['lodash', 'join']
      })
    ]
  };
```

这样就能很好的与 [tree shaking](https://webpack.docschina.org/guides/tree-shaking) 配合，将 `lodash` library 中的其余没有用到的导出去除。

## 细粒度 shim 

一些遗留模块依赖的 `this` 指向的是 `window` 对象。在接下来的用例中，调整我们的 `index.js`：

```diff
  function component() {
    var element = document.createElement('div');

    element.innerHTML = join(['Hello', 'webpack'], ' ');
+
+   // 假设我们处于 `window` 上下文
+   this.alert('Hmmm, this probably isn\'t a great idea...')

    return element;
  }

  document.body.appendChild(component());
```

当模块运行在 CommonJS 上下文中，这将会变成一个问题，也就是说此时的 `this` 指向的是 `module.exports`。在这种情况下，你可以通过使用 [`imports-loader`](https://webpack.docschina.org/loaders/imports-loader/) 覆盖 `this` 指向：

**webpack.config.js**

```diff
  const path = require('path');
  const webpack = require('webpack');

  module.exports = {
    ...
    
+   module: {
+     rules: [
+       {
+         test: require.resolve('index.js'),
+         use: 'imports-loader?this=>window'
+       }
+     ]
+   },
    ...
    
  };
```

## 全局 export 

让我们假设，某个 library 创建出一个全局变量，它期望 consumer(使用者) 使用这个变量。为此，我们可以在项目配置中，添加一个小模块来演示说明：

**project**

```diff
  webpack-demo
  |- package.json
  |- webpack.config.js
  |- /dist
  |- /src
    |- index.js
+   |- globals.js
  |- /node_modules
```

**src/globals.js**

```js
var file = 'blah.txt';
var helpers = {
  test: function() { console.log('test something'); },
  parse: function() { console.log('parse something'); }
};
```

你可能从来没有在自己的源码中做过这些事情，但是你也许遇到过一个老旧的 library，和上面所展示的代码类似。在这种情况下，我们可以使用 [`exports-loader`](https://webpack.docschina.org/loaders/exports-loader/)，将一个全局变量作为一个普通的模块来导出。例如，为了将 `file` 导出为 `file` 以及将 `helpers.parse` 导出为 `parse`，做如下调整：

**webpack.config.js**

```diff
  const path = require('path');
  const webpack = require('webpack');

  module.exports = {
    ...
    
    module: {
      rules: [
        ...
        
+       {
+         test: require.resolve('globals.js'),
+         use: 'exports-loader?file,parse=helpers.parse'
+       }
      ]
    },
    ...
    
  };
```

现在，在我们的 entry 入口文件中（即 `src/index.js`），我们能 `import { file, parse } from './globals.js';` ，然后一切将顺利运行。

## 加载 polyfill 

有很多方法来加载 polyfill。例如，想要引入 [`babel-polyfill`](https://babel.docschina.org/docs/en/babel-polyfill/) 我们只需如下操作：

```bash
npm install --save babel-polyfill
```

然后，使用 `import` 将其引入到我们的主 bundle 文件：

> 注意，我们没有将 `import` 绑定到某个变量。这是因为 polyfill 直接基于自身执行，并且是在基础代码执行之前，这样通过这些预置，我们就可以假定已经具有某些原生功能。

注意，这种方式优先考虑正确性，而不考虑 bundle 体积大小。为了安全和可靠，polyfill/shim 必须**运行于所有其他代码之前**，而且需要同步加载，或者说，需要在所有 polyfill/shim 加载之后，再去加载所有应用程序代码。 社区中存在许多误解，即现代浏览器“不需要”polyfill，或者 polyfill/shim 仅用于添加缺失功能 - 实际上，它们通常用于*修复损坏实现(repair broken implementation)*，即使是在最现代的浏览器中，也会出现这种情况。 因此，最佳实践仍然是，不加选择地和同步地加载所有 polyfill/shim，尽管这会导致额外的 bundle 体积成本。

如果你认为自己已经打消这些顾虑，并且希望承受损坏的风险。那么接下来的这件事情，可能是你应该要做的： 我们将会把 `import` 放入一个新文件，并加入 [`whatwg-fetch`](https://github.com/github/fetch) polyfill：

```bash
npm install --save whatwg-fetch
```

**src/index.js**

```diff
- import 'babel-polyfill';
```

**project**

```diff
  webpack-demo
  |- package.json
  |- webpack.config.js
  |- /dist
  |- /src
    |- index.js
    |- globals.js
+   |- polyfills.js
  |- /node_modules
```

**src/polyfills.js**

```javascript
import 'babel-polyfill';
import 'whatwg-fetch';
```

**webpack.config.js**

```diff
  const path = require('path');
  const webpack = require('webpack');

  module.exports = {
-   entry: './src/index.js',
+   entry: {
+     polyfills: './src/polyfills.js',
+     index: './src/index.js'
+   },
    output: {
-     filename: 'bundle.js',
+     filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    },
    ...
    
  };
```

如上配置之后，我们可以在代码中添加一些逻辑，条件地加载新的 `polyfills.bundle.js` 文件。根据需要支持的技术和浏览器来决定是否加载。我们将做一些简单的试验，来确定是否需要引入这些 polyfill：

**dist/index.html**

```diff
  <!doctype html>
  <html>
    <head>
      <title>Getting Started</title>
+     <script>
+       var modernBrowser = (
+         'fetch' in window &&
+         'assign' in Object
+       );
+
+       if ( !modernBrowser ) {
+         var scriptElement = document.createElement('script');
+
+         scriptElement.async = false;
+         scriptElement.src = '/polyfills.bundle.js';
+         document.head.appendChild(scriptElement);
+       }
+     </script>
    </head>
    <body>
      <script src="index.bundle.js"></script>
    </body>
  </html>
```

现在，在 entry 入口文件中，可以通过 `fetch` 获取一些数据：

**src/index.js**

```diff
  function component() {
    var element = document.createElement('div');

    element.innerHTML = join(['Hello', 'webpack'], ' ');

    return element;
  }

  document.body.appendChild(component());
+
+ fetch('https://jsonplaceholder.typicode.com/users')
+   .then(response => response.json())
+   .then(json => {
+     console.log('We retrieved some data! AND we\'re confident it will work on a variety of browser distributions.')
+     console.log(json)
+   })
+   .catch(error => console.error('Something went wrong when fetching this data: ', error))
```

执行构建脚本，可以看到，浏览器发送了额外的 `polyfills.bundle.js` 文件请求，然后所有代码顺利执行。注意，以上的这些设定可能还会有所改进，这里我们向你提供一个很棒的想法：将 polyfill 提供给需要引入它的用户。

## 进一步优化

### babel-preset-env？？？

## Node 内置 ？？？

像 `process` 这种 Node 内置模块，能直接根据配置文件进行正确的 polyfill，而不需要任何特定的 loader 或者 plugin。

## 其他工具 

### script-loader？？？

> 

最后，一些模块支持多种 [模块格式](https://webpack.docschina.org/concepts/modules)，例如一个混合有 AMD、CommonJS 和 legacy(遗留) 的模块。在大多数这样的模块中，会首先检查 `define`，然后使用一些怪异代码导出一些属性。在这些情况下，可以通过 [`imports-loader`](https://webpack.docschina.org/loaders/imports-loader/) 设置 `define=>false` 来强制 CommonJS 路径。



# 渐进式网络应用程序 ？？？



# 环境变量

想要消除 [开发环境](https://webpack.docschina.org/guides/development) 和 [生产环境](https://webpack.docschina.org/guides/production) 之间的 `webpack.config.js` 差异，你可能需要环境变量(environment variable)。

webpack 命令行 [环境配置](https://webpack.docschina.org/api/cli/#environment-options) 的 `--env` 参数，可以允许你传入任意数量的环境变量。而在 `webpack.config.js` 中可以访问到这些环境变量。例如，`--env.production` 或 `--env.NODE_ENV=local`（`NODE_ENV` 通常约定用于定义环境类型，查看 [这里](https://dzone.com/articles/what-you-should-know-about-node-env)）。

```bash
webpack --env.NODE_ENV=local --env.production --progress
```

> 如果设置 `env` 变量，却没有赋值，`--env.production` 默认表示将 `--env.production` 设置为 `true`。还有许多其他可以使用的语法。更多详细信息，请查看 [webpack CLI](https://webpack.docschina.org/api/cli/#environment-options) 文档。

对于我们的 webpack 配置，有一个必须要修改之处。通常，`module.exports` 指向配置对象。要使用 `env` 变量，你必须将 `module.exports` 转换成一个函数：

**webpack.config.js**

```js
const path = require('path');

module.exports = env => {
  // Use env.<YOUR VARIABLE> here:
  console.log('NODE_ENV: ', env.NODE_ENV); // 'local'
  console.log('Production: ', env.production); // true

  return {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
};
```



#  构建性能？？？



# Loader

常用loader：

## 文件

### raw-loader

加载文件原始内容（utf-8）

```bash
npm install --save-dev raw-loader
```

#### 用法

通过 webpack 配置、命令行或者内联使用 loader。

webpack.config.js

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.txt$/,
        use: 'raw-loader'
      }
    ]
  }
}
```

通过命令行（CLI）

```bash
webpack --module-bind 'txt=raw-loader'
```

内联使用

在你的项目中

```js
import txt from 'raw-loader!./file.txt';
```

### val-loader？？？

### url-loader

将文件加载为`base64`编码的URL

`url-loader` 功能类似于 [`file-loader`](https://github.com/webpack-contrib/file-loader)，但是在文件大小（单位 byte）低于指定的限制时，可以返回一个 DataURL。

```console
$ npm install url-loader --save-dev
```

```js
import img from './image.png'
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/i,
        use: [
          {
            loader: 'url-loader',
            options: {
                //字节
              limit: 8192
            }
          }
        ]
      }
    ]
  }
}
```

#### Options 

##### `1、fallback` 

Type: `String` Default: `'file-loader'`

指定当target文件size大于limit的时候使用的loader

```js
// webpack.config.js
{
  loader: 'url-loader',
  options: {
    fallback: 'responsive-loader'
  }
}
```

##### `2、limit` 

Type: `Number` Default: `undefined`

单位字节，target文件小于limit，返回base64；如果大于limit，默认使用file-loader或fallback指定的loader。

默认limit为无穷大，都转为base64.

```js
// webpack.config.js
{
  loader: 'url-loader',
  options: {
    limit: 8192
  }
}
```

##### `3、mimetype` 

Type: `String` Default: `(file extension)`

MIME类型

```js
// webpack.config.js
{
  loader: 'url-loader',
  options: {
    mimetype: 'image/png'
  }
}
```

### file-loader

解析import`/`require()文件为url，并且将文件发送到输出目录。

## JSON

## 转译

## 模板

## 样式

## 代码检查和测试

## 框架







# 入口(entry)

* 指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。
* 进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

## 配置

entry 属性，默认值为 ./src

### 单入口写法
module.exports = {
  entry: './path/to/my/entry/file.js'
};

