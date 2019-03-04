# 概念

本质上，webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。

示意图：https://www.webpackjs.com/

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

```
npm install --save-dev webpack
```

安装指定版本

```
npm install --save-dev webpack@<version>
```

如果你使用 webpack 4+ 版本，你还需要安装 CLI。

```
npm install --save-dev webpack-cli
```

# Getting started

创建项目目录。

执行如下命令:

```
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

## Manifest?????????????????????????????????????????????????????

# 开发

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
现在，我们可以在命令行中运行 npm start，就会看到浏览器自动加载页面。如果现在修改和保存任意源文件，web 服务器就会自动重新加载编译后的代码。

#### Hot Module Replacement?????????????????????????????????????????????????????????

# 入口(entry)

* 指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。
* 进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

## 配置

entry 属性，默认值为 ./src

### 单入口写法
module.exports = {
  entry: './path/to/my/entry/file.js'
};

# CSS提取
# LESS
# SASS
# POSTCSS
# html-loader
# image-webpack-loader
# url-loader
