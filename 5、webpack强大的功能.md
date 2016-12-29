### 图片暂时缺失，抽空补上
## 一切皆模块
Webpack 有一个不可不说的优点，它把所有的文件都可以当做模块处理，包括你的==JavaScript代码==，也包==括CSS和fonts以及图片==等等等，只有通过合适的loaders，它们都可以被当做模块被处理。

### 生成Source Maps（使调试更容易）
> 在学习阶段以及在小到中性的项目上，eval-source-map是一个很好的选项，不过记得只在开发阶段使用它，生产阶段一定不要使用这个选项。

1. source-map

在一个单独的文件中产生一个完整且功能完全的文件。这个文件具有最好的source map，但是它会减慢打包文件的构建速度；

2. cheap-module-source-map

在一个单独的文件中生成一个不带列映射的map，不带列映射提高项目构建速度，但是也使得浏览器开发者工具只能对应到具体的行，不能对应到具体的列（符号），会对调试造成不便；

3.  ==eval-source-map==

使用eval打包源文件模块，在同一个文件中生成干净的完整的source map。这个选项可以在不影响构建速度的前提下生成完整的sourcemap，但是对打包后输出的JS文件的执行具有性能和安全的隐患。不过在开发阶段这是一个非常好的选项，但是在生产阶段一定不要用这个选项；

4. cheap-module-eval-source-map
这是在打包文件时最快的生成source map的方法，生成的Source Map 会和打包后的JavaScript文件同行显示，没有列映射，和eval-source-map选项具有相似的缺点；

### 使用webpack构建本地服务器
> 让你的浏览器监测你都代码的修改，并自动刷新修改后的结果。不过它是一个单独的组件，在webpack中进行配置之前需要单独安装它作为项目依赖。
```
npm install --save-dev webpack-dev-server
```

### 构建本地服务器
```
npm install --save-dev webpack-dev-server
```

### Loaders，Modules，Plugins，缓存
1. Json
通过使用不同的loaders，webpack能对各种各样格式的文件进行处理。
> 分析JSON文件并把它转换为JS文件；把下一代的JS文件(ES6, ES7)转换为现代浏览器可识的JS文件；把React的JSX文件转换为JS文件。

Loaders跟Webpack-dev-server都是需要独立安装的，并且需要在webpack.config.js配置文件的modules关键字下进行配置(数组):
- test：一个匹配loaders所处理的文件的拓展名的正则表达式（必须）
- loader：loader的名称（必须）
- include/exclude: 手动添加必须处理的文件（文件夹）或屏蔽不需要处理的文件（文件夹）（可选）；
- query：为loaders提供额外的设置选项（可选）
```
// 安装json-loader
npm install --save-dev json-loader
```
```
// 配置webpack.config.js
module.exports = {
  ...
  module: {//在配置文件里添加JSON loader
    loaders: [
      {
        test: /\.json$/,
        loader: "json"
      }
    ]
  }
}
```
```
// 创建JSON文件list.json
{
  "greetText": "Hi there and greetings from JSON!"
}
```
```
// gretter.js
var list = require('./list.json')
module.exports = function () {
    var greet = document.createElement('div');
    greet.textContent = list.greetText;
    return greet;
}
```
2. Babel
这个loader主要用于:
- 将下一代的JS文件转换为当前浏览器可识别的JS文件
- 将React的JSX转换为JS

这个loader里有几个模块化的包，这些包都需要单独安装（用得最多的是解析Es6的babel-preset-es2015包和解析JSX的babel-preset-react包）。

```
// npm一次性安装多个依赖模块，模块之间用空格隔开
npm install --save-dev babel-core babel-loader babel-preset-es2015 babel-preset-react
```
```
// 这里主要以es6的示例为主
// 配置webpack.config.js
module.exports = {
  ...
  module: {//在配置文件里添加JSON loader
    loaders: [
      {
        test: /\.json$/,
        loader: "json"
      },
      {
        test: /\.js$/,
        loader: "babel",
        query: {
          presets: ["es2015"]
        }
      }
    ]
  }
}
```
```
// 可以使用es6的导入了，结合common.js的模块规范
import list from './list.json'
module.exports = function () {
	var greet = document.createElement('div')
	greet.textContent = list.greetings
	return greet
}
```

2.1 .babelrc
> Babel其实可以完全在webpack.config.js中进行配置，但是考虑到babel具有非常多的配置选项，在单一的webpack.config.js文件中进行配置往往使得这个文件显得太复杂，因此支持把babel的配置选项提取出相关部分，放在一个单独的名为 ".babelrc" 的配置文件中。

```
// 配置webpack.config.js
module.exports = {
  ...
  module: {
    loaders: [
      {
        test: /\.json$/,
        loader: "json"
      },
      {
        test: /\.js$/,
        loader: "babel",
        exclude: "/node_modules/"  // 在这个目录下的js文件均与业务无关，所以排除
      }
    ]
  }
}
```
```
// 配置.babelrc
// 把原来query项提取出来
{
  "presets": ["react", "es2015"]
}
```

3. CSS
webpack提供两个工具处理样式表，css-loader 和 style-loader
- css-loader使你能够使用类似@import 和 url(...)的方法，实现Common.js的require模块引入功能 => 能够用es6的import引入。
- style-loader将所有的计算后的样式加入页面中。

二者一起使用等同于将css当成js模块来写，并且能把样式表嵌入webpack打包后的JS文件中。

```
// npm一次性安装多个依赖模块，模块之间用空格隔开
npm install --save-dev style-loader css-loader
```
注：==感叹号的作用在于使同一文件能够使用不同类型的loader==
```
// 配置webpack.config.js
module.exports = {
  ...
  module: {
    loaders: [
      {
        test: /\.json$/,
        loader: "json"
      },
      {
        test: /\.js$/,
        loader: "babel",
        exclude: "/node_modules/"  // 在这个目录下的js文件均与业务无关，所以排除
      },
	  {
	    test: /\.css$/,
		loader: "style!css"  //优先用css-loader处理，其次用style-loader处理
	  }
    ]
  }
}
```
```
// 测试用CSS文件
body {
  background-color: pink;
  margin: 0;
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
}
```
```
// 在main.js里面直接导入CSS文件，相当于使用require
import "./main.css"
var temp = require('./Greeter.js')
document.getElementById('root').appendChild(temp())
```
==++通常情况下，css会和js打包到同一个文件中++==，并不会打包为一个单独的css文件，不过通过合适的配置 ==++webpack也可以把css打包为单独的文件的++==。

3.1. CSS模块
> 大方向，详情可查阅相关资料

在CSS loader中进行配置后，你所需要做的一切就是把”modules“传递到所需要的地方，然后就可以直接把CSS的类名传递到组件的代码中，且这样做只对当前页面/组件有效，不必担心在不同的页面/组件中具有相同的类名可能会造成冲突问题。
```
// 官方文档
https://github.com/css-modules/css-modules
```
```
// 配置webpack.config.js
module.exports = {
  ...
  module: {
    loaders: [
      {
        test: /\.json$/,
        loader: "json"
      },
      {
        test: /\.js$/,
        loader: "babel",
        exclude: "/node_modules/"
      },
	  {
	    test: /\.css$/,
		loader: "style!css?modules"  //比前面加上了?module，表示用到CSS模块
	  }
    ]
  }
}
```
```
// 创建CSS模块 CssModule.css
// 看似跟普通CSS文件差不多
.root {
  background-color: #eee;
  padding: 10px;
  border: 3px solid #ccc;
}
```
```
// 配置main.js
import styles from "./CssModule.css"
// 返回的是一个Object，键为root，值为编译后的所有样式属性
console.log(styles) // {root: "_2o6lrpNEKylW6Afk1Tgkyq"}

var temp = require('./Greeter.js')
document.getElementById('root').className = styles.root
document.getElementById('root').appendChild(temp())
```

3.2. CSS预处理器
> 需要强调的是Postcss跟Babel一样都是独立的平台
- sass
- less
- stylus
- postcss

上述安装对应loader即可，不再举例了。。

4. Plugins

插件Plugins是一个数组，肯定是需要安装的啦。
```
// 以下是webpack自带的实现版权声明的插件
var path = require("path");
var webpack = require("webpack");
console.log('yes')
module.exports = {
  entry:{
		app: ["./src/main.js"]
  },
  output:{
	  path:path.resolve(__dirname,"dist"),
	  // 虚拟文件所在的路径
	  publicPath: '/test/assets/',
	  filename:"bundle.js"
	},
	module: {},
	devServer: {
		contentBase: 'dist/',
		publicPath: '/test/assets/',
		hot: true,
		inline: true,
		stats: {
			colors: true
		}
	},
	plugins: [
        new webpack.HotModuleReplacementPlugin(),
        new webpack.BannerPlugin("Copyright Ljm inc.")
	]
}
```
![image](C:\Users\liangjm\Desktop\webpackExec\copyright.png)

4.1. HtmlWebpackPlugin

这个插件的作用是依据一个简单的模板，帮你生成最终的Html5文件，这个文件中自动引用了你打包后的JS文件。每次编译都在文件名中插入一个不同的哈希值。
```
// 安装插件
npm install --save-dev html-webpack-plugin
```

```
// 配置模板文件，也是html，而且在这里可以扩展为用handlebars,ejs等
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Webpack Sample Project</title>
  </head>
  <body>
    <div id='root'>
    </div>
  </body>
</html>
```

```
// 配置webpack.config.js
var path = require("path");
var webpack = require("webpack");
var HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  entry:{
		app: ["./src/main.js"]
  },
  output:{
	  path:path.resolve(__dirname,"build"),
	  // 虚拟文件所在的路径
	  publicPath: '/test/assets/',
	  filename:"bundle.js"
	},
	module: {
		loaders: [
			{
				test: /\.json$/,
				loader: "json"
			},
			{
				test: /\.js$/,
				loader: "babel"
			},
			{
				test: /\.css$/,
				loader: "style!css!postcss"  
			}
		]
	},
	devServer: {
		contentBase: 'template/',
		publicPath: '/test/assets/',
		hot: true,
		inline: true,
		clientLogLevel: 'info',
		open: 'http://localhost:8080',
		stats: {
			colors: true
		}
	},
	plugins: [
    new webpack.HotModuleReplacementPlugin(),
    new webpack.BannerPlugin("Copyright Ljm."),
    new HtmlWebpackPlugin({
    	template: __dirname + "/template/index.html"
    })
	]
}
```

如果需要把生成的模板文件编译出来，必须先这样做:
```
// cmd 
// 先用webpack打包构建，在对应目录下生成文件，然后再启用webpack-dev-server打开浏览器实现代码的热更新。
webpack && webpack-dev-server
```

目录结构如下：
> 这里的build目录纯属是作为存放模板生成的地方，这里只关注于经过命令行的webpack能够把模板编译生成出来放在这个目录就可以了。

![image](C:\Users\liangjm\Desktop\webpackExec\list.png)

4.2. 其他常用插件
- OccurenceOrderPlugin :为组件分配ID，通过这个插件webpack可以分析和优先考虑使用最多的模块，并为它们分配最小的ID.
- UglifyJsPlugin：压缩JS代码.
- ExtractTextPlugin：分离CSS和JS文件.

OccurenceOrder 和 UglifyJS plugins 都是内置插件，需要安装的只有ExtractTextPlugin
```
// 安装依赖
npm install --save-dev extract-text-webpack-plugin
```
```
// 配置webpack.config.js
var path = require("path");
var webpack = require("webpack");
var HtmlWebpackPlugin = require('html-webpack-plugin');
var ExtractTextPlugin = require('extract-text-webpack-plugin');

module.exports = {
  entry:{
		app: ["./src/main.js"]
  },
  output:{
	path:path.resolve(__dirname,"build"),
	  // 虚拟文件所在的路径
	  publicPath: '/test/assets/',
	  filename:"bundle.js"
	},
	module: {},
	devServer: {},
	plugins: [
            new webpack.HotModuleReplacementPlugin(),
            new webpack.BannerPlugin("Copyright Ljm."),
            new HtmlWebpackPlugin({
            	template: __dirname + "/template/index.html"
            }),
            new webpack.optimize.OccurenceOrderPlugin(),
            new webpack.optimize.UglifyJsPlugin(),
            new ExtractTextPlugin("main.css")
	]
}
```

### 缓存
- 使用缓存的最好方法是保证你的文件名和文件内容是匹配的（内容改变，名称相应改变）。
- webpack可以把一个哈希值添加到打包的文件名中，使用方法如下,添加特殊的字符串混合体（[name], [id] and [hash]）到输出文件名前。


