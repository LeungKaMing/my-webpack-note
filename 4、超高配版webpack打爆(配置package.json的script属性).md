### 超高配webpack打包(配置package.json的script字段)
对其做以下配置后，可以通过npm start直接代替上面的webpack命令，不用再考虑是全局还是局部安装的webpack。
```
{
  "name": "webpack-sample-project",
  "version": "1.0.0",
  "description": "Sample webpack project",
  "scripts": {
    "start": "webpack" //配置的地方就是这里啦，相当于把npm的start命令指向webpack命令
  },
  "author": "zhang",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^1.12.9"
  }
}
```
> npm的start是一个特殊的脚本名称，它的特殊性表现在，在命令行中使用 ==++npm start++== 就可以执行相关命令，如果对应的此脚本名称不是start，想要在命令行中运行时，需要这样用npm run {script name}如 ==++npm run build++==。