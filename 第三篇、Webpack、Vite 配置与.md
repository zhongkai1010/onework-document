# 第三篇、Webpack、Vite 配置与使用
指南：[https://www.webpackjs.com/guides/](https://www.webpackjs.com/guides/)

基本配置

entry（入口）

指示 Webpack 从哪个文件开始打包

output（输出）

指示 Webpack 打包完的文件输出到哪里去，如何命名等

loader（加载器）

webpack 本身只能处理 js、json 等资源，其他资源需要借助 loader，Webpack 才能解析

plugins（插件）

扩展 Webpack 的功能

mode（模式）

## HtmlWebpackPlugin
`HtmlWebpackPlugin`简化了HTML文件的创建，以便为你的webpack包提供服务。这对于在文件名中包含每次会随着编译而发生变化哈希的 webpack bundle 尤其有用。 你可以让插件为你生成一个HTML文件，使用lodash模板提供你自己的模板，或使用你自己的loader。

```javascript
var HtmlWebpackPlugin = require('html-webpack-plugin');
var path = require('path');

var webpackConfig = {
  entry: 'index.js',
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: 'index_bundle.js'
  },
  plugins: [new HtmlWebpackPlugin()]
};
```
这将会产生一个包含以下内容的文件 `dist/index.html`：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>webpack App</title>
  </head>
  <body>
    <script src="index_bundle.js"></script>
  </body>
</html>
```
# webpack配置html热更新问题
再’./src/index.html’引入html文件作为依赖

//两个文件在同一目录下 import './index.html'