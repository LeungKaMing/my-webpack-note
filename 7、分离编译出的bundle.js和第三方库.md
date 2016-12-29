### 7、分离编译出的bundle.js和第三方库 (code-spilting)
> 两大页面的优化方法：1、压缩 2、抽取公共模块

现在我们打包出来的只有一个bundle.js，如果第三方库很多的话，会造成这个文件非常大，减慢加载速度，所以需要把第三方库和我们打包出来的代码分成两个文件。
#### 单个入口
##### 修改entry入口文件
```
  entry: {
    app: path.resolve(APP_PATH, 'index.js'),
    //添加要打包在vendors里面的库
    vendors: ['jquery', 'moment']
  },
  output: {
    path: __dirname + '/dist',
    filenmae: 'bundle.js'
  }
```
##### 添加CommonsChunkPlugin
```
  plugins: [
    //这个使用uglifyJs压缩你的js代码
    new webpack.optimize.UglifyJsPlugin({minimize: true}),,
    new HtmlwebpackPlugin({
      title: 'Hello World app',
      chunk: ['vendors']
    }),
    //把入口文件里面的数组打包成verdors.js
    new webpack.optimize.CommonsChunkPlugin(/* chunkName= */"vendors", /* filename= */'vendors.js')
  ]
```
##### 结果
它会把所有index.js里面

#### 多个入口
引用模块分两种情况:
1. 指定抽离哪些模块
2. 没有指定抽离哪些模块
3. 业务模块，类库，框架三者各自抽离
 
第一种情况：
```
... 省略 ...
module.exports = {
  devtool: 'eval-source-map',
  entry:{
    // 多个入口，为什么用键值？键名是为了给下面htmlWebpackPlugins插件里的模板自动引入
  	main: ['./src/main.js'],
  	main2: ['./src/main2.js'],
  	// 需要单独分离出的模块
  	chunk: ['jquery']
  },
  output:{
	path:path.resolve(__dirname,"dist"),
	// 虚拟文件所在的路径
	publicPath: '/test/assets/',
        filename:"[name].js"
  },
	devServer: {
		contentBase: 'dist/html/',
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
	        // 指定生成模板的位置[可带上路径 / 名字]
	    	filename: __dirname + "/dist/html/index.html",
	    	// 源模板所在的位置[可带上路径 / 名字]
	    	template: __dirname + "/template/index.html",
	    	// 注入点
	    	inject: 'body',
            // 将入口的键代入就会把对应的库/模块自动加在生成的模板里
	    	chunks: ['main', 'chunk']
	    }),
	    new HtmlWebpackPlugin({
	        // 指定生成模板的位置[可带上路径 / 名字]
	    	filename: __dirname + "/dist/html/index2.html",
	    	// 源模板所在的位置[可带上路径 / 名字]
	    	template: __dirname + "/template/index2.html",
	    	// 注入点
	    	inject: 'body',
            // 将入口的键代入就会把对应的库/模块自动加在生成的模板里
	    	chunks: ['main2', 'chunk']
	    }),
	    // 【将入口处的chunk键值（数组）抽离出来，放进chunk.js文件里】
	    new webpack.optimize.CommonsChunkPlugin('chunk', 'chunk.js'),
	    new webpack.ProvidePlugin({
	    $: "jquery",
	        jQuery: "jquery",
	        "window.jQuery": "jquery"
	    }),
	]
}
```
第二个情况：
```
... 省略 ...
module.exports = {
  devtool: 'eval-source-map',
  entry:{
    // 多个入口
  	main: ['./src/main.js'],
  	main2: ['./src/main2.js']
  },
  output:{
	path:path.resolve(__dirname,"dist"),
	// 虚拟文件所在的路径
	publicPath: '/test/assets/',
        filename:"[name].js"
  },
	devServer: {
		contentBase: 'dist/html/',
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
	        // 指定生成模板的位置[可带上路径 / 名字]
	    	filename: __dirname + "/dist/html/index.html",
	    	// 源模板所在的位置[可带上路径 / 名字]
	    	template: __dirname + "/template/index.html",
	    	// 注入点
	    	inject: 'body',
            // 将入口的键代入就会把对应的库/模块自动加在生成的模板里
	    	chunks: ['main', 'chunk']
	    }),
	    new HtmlWebpackPlugin({
	        // 指定生成模板的位置[可带上路径 / 名字]
	    	filename: __dirname + "/dist/html/index2.html",
	    	// 源模板所在的位置[可带上路径 / 名字]
	    	template: __dirname + "/template/index2.html",
	    	// 注入点
	    	inject: 'body',
            // 将入口的键代入就会把对应的库/模块自动加在生成的模板里
	    	chunks: ['main2', 'chunk']
	    }),
	    // 【自动把多个入口内重复引用的块抽取出来，抽取条件是这些块在所有入口出现的次数】
	    new webpack.optimize.CommonsChunkPlugin({
        	// 大于或等于这个值的时候才将入口文件所用到的公共模块进行抽离
	        minChunks: 2,
	        name: chunk
	    }),
	    new webpack.ProvidePlugin({
	    $: "jquery",
	        jQuery: "jquery",
	        "window.jQuery": "jquery"
	    }),
	]
}
```

第三种情况：
```
... 省略 ...
module.exports = {
  devtool: 'eval-source-map',
  entry:{
  	main: ['./src/main.js'],
  	main2: ['./src/main2.js'],
  	jquery: ['jquery'],
  	vue: ['vue']
  },
  output:{
	path:path.resolve(__dirname,"dist"),
	// 虚拟文件所在的路径
	publicPath: '/test/assets/',
        filename:"[name].js"
  },
  devServer: {
    contentBase: 'dist/html/',
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
      // 指定生成模板的位置[可带上路径 / 名字]
      filename: __dirname + "/dist/html/index.html",
	  // 源模板所在的位置[可带上路径 / 名字]
	  template: __dirname + "/template/index.html",
	  // 注入点
	  inject: 'body',
      // 将入口的键代入就会把对应的库/模块自动加在生成的模板里
	  chunks: ['main', 'chunk']
	}),
	new HtmlWebpackPlugin({
	  // 指定生成模板的位置[可带上路径 / 名字]
	  filename: __dirname + "/dist/html/index2.html",
	  // 源模板所在的位置[可带上路径 / 名字]
	  template: __dirname + "/template/index2.html",
	  // 注入点
	  inject: 'body',
      // 将入口的键代入就会把对应的库/模块自动加在生成的模板里
	  chunks: ['main2', 'chunk']
	}),
	// 【将入口处的chunk键值抽离出来，放进chunk.js文件里】
	new webpack.optimize.CommonsChunkPlugin({
	  name: ['jquery', 'vue', 'load'],
	  minChunks: 2
	}),
	new webpack.ProvidePlugin({
	  $: "jquery",
	  jQuery: "jquery",
	  "window.jQuery": "jquery"
	}),
  ]
}
```
