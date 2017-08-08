这个仓库是 Webpack 的简单的 demo 集合。

这些 demo 用简单明了的方式写成。跟着文档学你能很容易就学会这个强大的工具

## 如何使用

首先, 全局安装 [Webpack](https://www.npmjs.com/package/webpack) 和 [webpack-dev-server](https://www.npmjs.com/package/webpack-dev-server) .

```bash
$ npm install -g webpack@1.x webpack-dev-server@1.x
```

然后, 将项目克隆到本地并安装项目的依赖

```bash
# Linux & Mac
$ git clone git@github.com:ruanyf/webpack-demos.git

# Windows
$ git clone https://github.com/ruanyf/webpack-demos.git
:
$ cd webpack-demos
$ npm install
```

现在，在项目中的 demo* 目录下演示源文件

```bash
$ cd demo01
$ webpack-dev-server
```

在你的浏览器中访问 http://127.0.0.1:8080 .

## 前言: Webpack 是什么

Webpack 是一个像 Grunt 和 Gulp 一样的前端构建系统。

它可以用作类似于 Browserify 的模块绑定器，并且做得[更多](http://webpack.github.io/docs/what-is-webpack.html).

```bash
$ browserify main.js > bundle.js
# be equivalent to
$ webpack main.js bundle.js
```

它的配置文件是 `webpack.config.js`.

```javascript
// webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
};
```

在使用 `webpack.config.js` 后，您可以在使用 `webpack` 命令时不使用任何参数

```bash
$ webpack
```

你应该知道的一些 Webpack 命令

- `webpack` – 用于在开发环境中构建
- `webpack -p` – 用于在生产环境中构建
- `webpack --watch` – 监控目录下的文件变化并实时构建
- `webpack -d` – 包含资源地图
- `webpack --colors` - 让编译的输出内容带有颜色

为了准备生成一个应用程序，您可以在 package.json 文件中写下` scripts` 字段，如下所示

```javascript
// package.json
{
  // ...
  "scripts": {
    "dev": "webpack-dev-server --devtool eval --progress --colors",
    "deploy": "NODE_ENV=production webpack -p"
  },
  // ...
}
```

## 索引

1. [入口文件](#demo01-入口文件-源代码)
1. [多个入口文件](#demo02-多个入口文件-源代码)
1. [Babel-loader](#demo03-babel-loader-源代码)
1. [CSS-loader](#demo04-css-loader-源代码)
1. [image-loader](#demo05-image-loader-源代码)
1. [CSS模块](#demo06-css模块-源代码)
1. [UglifyJs插件](#demo07-uglifyjs插件-源代码)
1. [加载第三方Webpack插件](#demo08-加载第三方Webpack插件-源代码)
1. [环境标签](#demo09-环境标签-源代码)
1. [代码拆分](#demo10-代码拆分-源代码)
1. [代码拆分和bundle-loader](#demo11-代码拆分和-bundle-loader-源代码)
1. [公共模块](#demo12-公共模块-源代码)
1. [第三方代码块](#demo13-第三方代码块-源代码)
1. [公共全局变量](#demo14-公共全局变量-源代码)
1. [模块热替换](#demo15-模块热替换-源代码)
1. [React路由](#demo16-react路由-源代码)

## Demo01: 入口文件 ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo01))

入口文件是一个 Webpack 将读取用来构建 bundle.js 的文件。

例如, `main.js` 是一个入口文件

```javascript
// main.js
document.write('<h1>Hello World</h1>');
```

index.html

```html
<html>
  <body>
    <script type="text/javascript" src="bundle.js"></script>
  </body>
</html>
```
Webpack 根据 `webpack.config.js` 中的配置信息来构建 `bundle.js`.

```javascript
// webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
};
```
启动服务, 访问 http://127.0.0.1:8080 .

```bash
$ webpack-dev-server
```

## Demo02: 多个入口文件 ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo02))

允许使用多个入口文件，它对于多页面应用程序很有用。

```javascript
// main1.js
document.write('<h1>Hello World</h1>');

// main2.js
document.write('<h2>Hello Webpack</h2>');
```

index.html

```html
<html>
  <body>
    <script src="bundle1.js"></script>
    <script src="bundle2.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: {
    bundle1: './main1.js',
    bundle2: './main2.js'
  },
  output: {
    filename: '[name].js'
  }
};
```

## Demo03: Babel-loader ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo03))

转换器是是转换应用程序资源文件的预处理程序 ([更多信息](http://webpack.github.io/docs/using-loaders.html)). 例如, [Babel-loader](https://www.npmjs.com/package/babel-loader) 
可以将 JSX / ES6 文件转换为 JS 文件。官方文档中有[转换器的完整列表](http://webpack.github.io/docs/list-of-loaders.html).

`main.jsx` 是一个 JSX 文件

```javascript
const React = require('react');
const ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.querySelector('#wrapper')
);
```

index.html

```html
<html>
  <body>
    <div id="wrapper"></div>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        loader: 'babel-loader?presets[]=es2015&presets[]=react'
      },
    ]
  }
};
```

在 `webpack.config.js` 中, `module.loaders` 字段用于分配转换器。 上面的片段使用 `babel-loader` 也需要插件 [babel-preset-es2015](https://www.npmjs.com/package/babel-preset-es2015) and [babel-preset-react](https://www.npmjs.com/package/babel-preset-react) 透过ES6和React。 您还可以采取其他方式设置babel查询选项。

```javascript
module: {
  loaders: [
    {
      test: /\.jsx?$/,
      exclude: /node_modules/,
      loader: 'babel-loader',
      query: {
        presets: ['es2015', 'react']
      }
    }
  ]
}
```

## Demo04: CSS-loader ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo04))

Webpack 允许您在 JS 文件中引入 CSS，然后使用 CSS-loader 预处理 CSS 文件。

main.js

```javascript
require('./app.css');
```

app.css

```css
body {
  background-color: blue;
}
```

index.html

```html
<html>
  <head>
    <script type="text/javascript" src="bundle.js"></script>
  </head>
  <body>
    <h1>Hello World</h1>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders:[
      { test: /\.css$/, loader: 'style-loader!css-loader' },
    ]
  }
};
```
注意，你必须使用两种转换器来转换CSS文件。 首先使用[CSS-loader](https://www.npmjs.com/package/css-loader) 来读取 CSS 文件，另一种是将样式标签插入到 HTML 页面中的[style-loader](https://www.npmjs.com/package/style-loader). 不同的转换器通过感叹号链接。

启动服务器后, `index.html` 将具有内部样式表。

```html
<head>
  <script type="text/javascript" src="bundle.js"></script>
  <style type="text/css">
    body {
      background-color: blue;
    }
  </style>
</head>
```

## Demo05: image-loader ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo05))

Webpack 也可以在 js 文件中引入图片

main.js

```javascript
var img1 = document.createElement("img");
img1.src = require("./small.png");
document.body.appendChild(img1);

var img2 = document.createElement("img");
img2.src = require("./big.png");
document.body.appendChild(img2);
```

index.html

```html
<html>
  <body>
    <script type="text/javascript" src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders:[
      { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192' }
    ]
  }
};
```

[url-loader](https://www.npmjs.com/package/url-loader) 转换图片文件. 如果图像尺寸小于8192个字节, 它将被转换成数据 Url; 除此之外, 它将被转换成正常的 Url. 如你所见, 问号（？）用于将参数传递给转换器。

启动服务器后，`small.png` 和 `big.png` 将具有如下 URL。

```html
<img src="data:image/png;base64,iVBOR...uQmCC">
<img src="4853ca667a2b8b8844eb2693ac1b2578.png">
```

## Demo06: CSS模块 ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo06))

`css-loader?modules` (查询参数模块) 启用 [CSS模块](https://github.com/css-modules/css-modules)规范.

这意味着您的模块默认情况下，CSS是局域范围的CSS. 您可以使用 `:global(...)`选择器或规则关闭. ([更多信息](https://css-modules.github.io/webpack-demo/))

index.html

```html
<html>
<body>
  <h1 class="h1">Hello World</h1>
  <h2 class="h2">Hello Webpack</h2>
  <div id="example"></div>
  <script src="./bundle.js"></script>
</body>
</html>
```

app.css

```css
.h1 {
  color:red;
}

:global(.h2) {
  color: blue;
}
```

main.jsx

```javascript
var React = require('react');
var ReactDOM = require('react-dom');
var style = require('./app.css');

ReactDOM.render(
  <div>
    <h1 className={style.h1}>Hello World</h1>
    <h2 className="h2">Hello Webpack</h2>
  </div>,
  document.getElementById('example')
);
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        query: {
          presets: ['es2015', 'react']
        }
      },
      {
        test: /\.css$/,
        loader: 'style-loader!css-loader?modules'
      }
    ]
  }
};
```

启动服务器

```bash
$ webpack-dev-server
```

访问 http://127.0.0.1:8080 ,你会发现只有第二个 `h1` 是红色的, 因为它的CSS范围是本地范围的, `h2` 是蓝色，因为它的CSS范围是全局范围的。

## Demo07: UglifyJs插件 ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo07))

Webpack有一个扩展其功能的插件系统。 例如，[UglifyJs插件](http://webpack.github.io/docs/list-of-plugins.html#uglifyjsplugin) 将会缩小输出 (`bundle.js`) 的JS代码。

main.js

```javascript
var longVariableName = 'Hello';
longVariableName += ' World';
document.write('<h1>' + longVariableName + '</h1>');
```

index.html

```html
<html>
<body>
  <script src="bundle.js"></script>
</body>
</html>
```

webpack.config.js

```javascript
var webpack = require('webpack');
var uglifyJsPlugin = webpack.optimize.UglifyJsPlugin;
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new uglifyJsPlugin({
      compress: {
        warnings: false
      }
    })
  ]
};
```

启动服务器后, `main.js` 将被细化为追随者

```javascript
var o="Hello";o+=" World",document.write("<h1>"+o+"</h1>")
```

## Demo08: 用于在浏览器中打开HTML文件的Webpack插件 ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo08))

这个demo向您展示如何加载第三方插件

[html-webpack-plugin](https://github.com/ampedandwired/html-webpack-plugin) 可以为你创建 `index.html`, [open-browser-webpack-plugin](https://github.com/baldore/open-browser-webpack-plugin) 的作用是当 Webpack 加载时，可以打开新的浏览器选项卡。

main.js

```javascript
document.write('<h1>Hello World</h1>');
```

webpack.config.js

```javascript
var HtmlwebpackPlugin = require('html-webpack-plugin');
var OpenBrowserPlugin = require('open-browser-webpack-plugin');

module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new HtmlwebpackPlugin({
      title: 'Webpack-demos',
      filename: 'index.html'
    }),
    new OpenBrowserPlugin({
      url: 'http://localhost:8080'
    })
  ]
};
```

运行  `webpack-dev-server`.

```bash
$ webpack-dev-server
```

现在你不需要手工编写 `index.html` 也不必自己打开浏览器。 Webpack自动为你做了所有这些事情。

## Demo09: 环境标签 ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo09))

您只能在具有环境标签的开发环境中启用一些代码

main.js

```javascript
document.write('<h1>Hello World</h1>');

if (__DEV__) {
  document.write(new Date());
}
```

index.html

```html
<html>
<body>
  <script src="bundle.js"></script>
</body>
</html>
```

webpack.config.js

```javascript
var webpack = require('webpack');

var devFlagPlugin = new webpack.DefinePlugin({
  __DEV__: JSON.stringify(JSON.parse(process.env.DEBUG || 'false'))
});

module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [devFlagPlugin]
};
```

现在将环境变量传递给webpack。

```bash
# Linux & Mac
$ env DEBUG=true webpack-dev-server

# Windows-cmd
$ set DEBUG=true
$ webpack-dev-server

# Windows-powershell
$ $env:DEBUG='true'
$ webpack-dev-server
```

## Demo10: 代码拆分 ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo10))

对于大型网络应用程序，将所有代码放入单个文件并不有效，Webpack允许您将它们分割成多个块。 特别是如果在某些情况下只需要一些代码块，则这些块可以按需加载。

首先，你使用 `require.ensure` 定义分割点。([官方文档](http://webpack.github.io/docs/code-splitting.html))

```javascript
// main.js
require.ensure(['./a'], function(require) {
  var content = require('./a');
  document.open();
  document.write('<h1>' + content + '</h1>');
  document.close();
});
```

`require.ensure` 告诉Webpack `./a.js` 应该从 `bundle.js` 中分开，并且内置到单个块文件中。

```javascript
// a.js
module.exports = 'Hello World';
```

现在 Webpack 负责依赖、输出文件和运行时。你不必把任何冗余放在你的 `index.html` 和 `webpack.config.js` 中。

```html
<html>
  <body>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
};
```

启动服务器

```bash
$ webpack-dev-server
```

表面上你不会看到任何差异. 然而，实际上 Webpack 在不同的块（`bundle.js` and `1.bundle.js`) 中构建 `main.js` 和 `a.js` 文件。当有需要时从 `bundle.js` 加载 `1.bundle.js`。

## Demo11: 代码拆分和 bundle-loader ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo11))

另一种代码拆分方式是使用 [bundle-loader](https://www.npmjs.com/package/bundle-loader).

```javascript
// main.js

// 现在请求a.js，它将会被绑定到另一个文件中
var load = require('bundle-loader!./a.js');

//等到 a.js 可用（并获得导出）
//你需要异步等待它
load(function(file) {
  document.open();
  document.write('<h1>' + file + '</h1>');
  document.close();
});
```

`require('bundle-loader!./a.js')` 告诉 Webpack 从另一个块加载 `a.js`

现在 Webpack 将在 `bundle.js` 中构建 `main.js`，在 `1.bundle.js` 中构建 `a.js`

## Demo12: 公共模块 ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo12))

当多个脚本具有公共模块时，可以使用 CommonsChunkPlugin 将公共部分提取到单独的文件中

```javascript
// main1.jsx
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>Hello World</h1>,
  document.getElementById('a')
);

// main2.jsx
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(
  <h2>Hello Webpack</h2>,
  document.getElementById('b')
);
```

index.html

```html
<html>
  <body>
    <div id="a"></div>
    <div id="b"></div>
    <script src="init.js"></script>
    <script src="bundle1.js"></script>
    <script src="bundle2.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
var CommonsChunkPlugin = require("webpack/lib/optimize/CommonsChunkPlugin");
module.exports = {
  entry: {
    bundle1: './main1.jsx',
    bundle2: './main2.jsx'
  },
  output: {
    filename: '[name].js'
  },
  module: {
    loaders:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        query: {
          presets: ['es2015', 'react']
        }
      },
    ]
  },
  plugins: [
    new CommonsChunkPlugin('init.js')
  ]
}
```

## Demo13: 第三方代码块 ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo13))

您还可以从 CommonsChunkPlugin 中将脚本中的供应商库解压缩到单独的文件中

main.js

```javascript
var $ = require('jquery');
$('h1').text('Hello World');
```

index.html

```html
<html>
  <body>
    <h1></h1>
    <script src="vendor.js"></script>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
var webpack = require('webpack');

module.exports = {
  entry: {
    app: './main.js',
    vendor: ['jquery'],
  },
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin(/* chunkName= */'vendor', /* filename= */'vendor.js')
  ]
};
```

如果您希望在每个模块中都可以使用模块作为变量，例如使 $ 和 jQuery 在每个模块中都可用，而无需编写 `require("jquery")`。你应该使用 `ProvidePlugin` ([官方文档](http://webpack.github.io/docs/shimming-modules.html)).

```javascript
// main.js
$('h1').text('Hello World');


// webpack.config.js
var webpack = require('webpack');

module.exports = {
  entry: {
    app: './main.js'
  },
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new webpack.ProvidePlugin({
      $: "jquery",
      jQuery: "jquery",
      "window.jQuery": "jquery"
    })
  ]
};
```

## Demo14: 公共全局变量 ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo14))

如果要使用一些全局变量，并且不想将它们包含在 Webpack 包中，可以在 `webpack.config.js` 中启用 `externals` 字段 ([官方文档](http://webpack.github.io/docs/library-and-externals.html)).

例如，我们有一个 `data.js`.

```javascript
var data = 'Hello World';
```

我们可以将 `data` 作为一个全局变量暴露出来

```javascript
// webpack.config.js
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        query: {
          presets: ['es2015', 'react']
        }
      },
    ]
  },
  externals: {
    // require('data') is external and available
    //  on the global var data
    'data': 'data'
  }
};
```

现在，您需要将 `data` 作为脚本中的模块变量。 但它实际上是一个全局变量。

```javascript
// main.jsx
var data = require('data');
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>{data}</h1>,
  document.body
);
```

## Demo15: 模块热替换 ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo15))

[模块热替换](https://github.com/webpack/docs/wiki/hot-module-replacement-with-webpack) (HMR) 在应用程序运行时更改，添加或删除模块，而不**重新加载页面**

你有 [两种方式](http://webpack.github.io/docs/webpack-dev-server.html#hot-module-replacement) 使用 webpack-dev-server 启用模块热替换

(1) 在命令行中指定 `--hot` 和 `--inline`

```bash
$ webpack-dev-server --hot --inline
```

一些选项的含义 

- `--hot`: 添加 HotModuleReplacementPlugin 并将服务器切换到热模式
- `--inline`: 将 webpack-dev-server 运行时嵌入到包中
- `--hot --inline`: 也可以添加 webpack/hot/dev-server 入口

(2) 修改 `webpack.config.js`.

- 将 `new webpack.HotModuleReplacementPlugin()` 添加到 `plugins` 字段中 
- 将 `webpack/hot/dev-server` 和 `webpack-dev-server/client?http://localhost:8080` 添加到 `entry` 字段中

`webpack.config.js` 看起来像下面的样子

```javascript
var webpack = require('webpack');
var path = require('path');

module.exports = {
  entry: [
    'webpack/hot/dev-server',
    'webpack-dev-server/client?http://localhost:8080',
    './index.js'
  ],
  output: {
    filename: 'bundle.js',
    publicPath: '/static/'
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin()
  ],
  module: {
    loaders: [{
      test: /\.jsx?$/,
      exclude: /node_modules/,
      loader: 'babel-loader',
      query: {
        presets: ['es2015', 'react']
      },
      include: path.join(__dirname, '.')
    }]
  }
};
```

现在启动 dev 服务器 

```bash
$ webpack-dev-server
```

访问 http://localhost:8080，在你的浏览器中你应该可以看到 'Hello World'。

不用关闭这个服务器，打开一个新的终端编辑 `App.js` 文件，并将 `Hello World` 修改为 `Hello Webpack`保存。并在浏览器中查看发生了什么变化

App.js

```javascript
import React, { Component } from 'react';

export default class App extends Component {
  render() {
    return (
      <h1>Hello World</h1>
    );
  }
}
```

index.js

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));
```

index.html

```html
<html>
  <body>
    <div id='root'></div>
    <script src="/static/bundle.js"></script>
  </body>
</html>
```

## Demo16: React路由 ([源代码](https://github.com/ruanyf/webpack-demos/tree/master/demo16))

这个 demo 使用 Webpack 构建 [React-router](https://github.com/rackt/react-router/blob/0.13.x/docs/guides/overview.md)的官方示例

让我们想象一下一个带有仪表板，收件箱和日历的应用程序。

```
+---------------------------------------------------------+
| +---------+ +-------+ +--------+                        |
| |Dashboard| | Inbox | |Calendar|      Logged in as Jane |
| +---------+ +-------+ +--------+                        |
+---------------------------------------------------------+
|                                                         |
|                        Dashboard                        |
|                                                         |
|                                                         |
|   +---------------------+    +----------------------+   |
|   |                     |    |                      |   |
|   | +              +    |    +--------->            |   |
|   | |              |    |    |                      |   |
|   | |   +          |    |    +------------->        |   |
|   | |   |    +     |    |    |                      |   |
|   | |   |    |     |    |    |                      |   |
|   +-+---+----+-----+----+    +----------------------+   |
|                                                         |
+---------------------------------------------------------+
```

```bash
$ webpack-dev-server --history-api-fallback
```

## 有用的链接

- [Webpack文档](http://webpack.github.io/docs/)
- [Webpack怎么样](https://github.com/petehunt/webpack-howto), by Pete Hunt
- [潜入Webpack](https://web-design-weekly.com/2014/09/24/diving-webpack/), by Web Design Weekly
- [Webpack和React非常棒](http://www.christianalfoni.com/articles/2014_12_13_Webpack-and-react-is-awesome), by Christian Alfoni
- [Browserify vs Webpack](https://medium.com/@housecor/browserify-vs-webpack-b3d7ca08a0a9), by Cory House
- [React Webpack cookbook](https://christianalfoni.github.io/react-webpack-cookbook/index.html), by Christian Alfoni

## 许可证

MIT
