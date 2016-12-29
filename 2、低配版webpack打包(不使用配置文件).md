### 初始化工作
```
//初始化npm
npm init -y
//全局安装
npm install -g webpack
//安装到你的项目目录
npm install --save-dev webpack
```
#### 按照以下目录来创建文件：
```
- root
-- src
    Greeter.js
    main.js
-- dist
    index.html
```
- Greeter.js (src目录下)
```
// 凡是挂载在exports这个属性上都是用于 当前模块导出的
module.exports = function() {
  var greet = document.createElement('div');
  greet.textContent = "Hi there and greetings!";
  return greet;
};
```
- main.js (src目录下)
```
var greeter = require('./Greeter.js');
document.getElementById('root').appendChild(greeter());
```
- index.html (dist目录下)
```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Webpack Sample Project</title>
  </head>
  <body>
    <div id='root'>
    </div>
    <script src="bundle.js"></script>
  </body>
</html>
```

#### 正式使用低配版webpack
```
webpack src/main.js dist/bundle.js
```
> 恭喜！此时你已经成功的使用Webpack通过命令行打包了一个文件了！