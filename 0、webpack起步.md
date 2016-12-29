### 认识webpack.config.js配置文件
```
var webpack = require('webpack');
var HtmlWebpackPlugin = require('html-webpack-plugin');
var ExtractTextPlugin = require('extract-text-webpack-plugin');

module.exports = {
  // 入口文件
  entry: __dirname + "/app/main.js",
  // 输出文件
  output: {
    // 输出路径
    path: __dirname + "/build",
    // 输出文件名
    filename: "[name]-[hash].js"
  },
  // 使用的加载器有哪些
  module: {
    loaders: [
      // 使json文件能够作为一个模块，方便我们用node的模块引入规范引入require
      {
        test: /\.json$/,
        loader: "json"
      },
      // 除了/node_modules这个目录，所有带js后缀的文件都兼容ES6的书写格式
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel'
      },
      // 使css文件能够作为一个模块，以@import的模块引入方式引入
      {
        test: /\.css$/,
        loader: ExtractTextPlugin.extract('style', 'css?modules!postcss')
      }
    ]
  },
  postcss: [
    require('autoprefixer')
  ],
  // 使用插件扩展
  plugins: [
    // 使用html模板的时候必须要用到这个插件
    new HtmlWebpackPlugin({
      template: __dirname + "/app/index.tmpl.html"
    }),
    new webpack.optimize.OccurenceOrderPlugin(),
    // 压缩代码
    new webpack.optimize.UglifyJsPlugin(),
    new ExtractTextPlugin("[name]-[hash].css")
  ]
}
```
