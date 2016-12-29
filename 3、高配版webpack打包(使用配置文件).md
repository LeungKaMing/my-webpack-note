### 为什么要使用配置文件？
> 可以应对更复杂的情况。
```
module.exports = {
	entry: __dirname + '/src/main.js', //入口文件
	output: {
		path: __dirname + '/dist', //打包后的文件存放的地方
		filename: 'bundle.js' //打包后输出文件的文件名
	}
}
```
> __dirname 是node.js中的一个全局变量，它指向当前执行脚本所在的目录。

#### 正式使用高配版webpack
```
// 没错，直接在命令行输入webpack就可以了
webpack
```