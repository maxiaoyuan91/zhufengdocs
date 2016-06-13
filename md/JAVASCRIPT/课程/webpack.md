## webpack
webpack是一款强大的模块加载器兼打包工具，它能把各种资源，例如JS（含JSX）、coffee、样式（含less/sass）、图片等都作为模块来使用和处理。

## 1. webpack初体验

### 1.1 初始化git
```javascript
$ mkdir webpack-demos && cd webpack-demos && git init
```

### 1.2 初始化项目
```bash
$ npm init -y
```


### 1.3 增加.gitignore
创建文件
```javascript
$ touch.gitignore
```

在文件中增加以下内容
```
node_modules 
.idea
```

### 1.4 在项目根目录下创建src和build目录
`src`目录存放源码，`build`目录存放编译打包之后的资源
```
$ mkdir src build
```

### 1.5 增加以下文件
#### 1.5.1 `src/component.js`
```javascript
$ cd src && touch component.js
```
```javascript
exports.name = 'zfpx';
```
#### 1.5.2 `src/index.js`
```javascript
$ cd src && touch index.js
```
```javascript
var comp = require('./component.js');
console.log(comp.name);
```
#### 1.5.3 `build/index.html`
```javascript
$ cd build && touch index.html
```
```javascript
<script src="bundle.js"></script>
```

#### 1.5.4 目录结构
![目录结构](/static/img/webpacks.jpg)

### 1.6 下载`webpack`和`webpack-dev-server`
```
$ npm install webpack --save-dev
```

### 1.7 创建webpack的配置文件
```javascript
$ touch webpack.config.js
```

配置`webpack.config.js`
```javascript
var path = require('path');
module.exports = {
    entry: path.resolve(__dirname, 'src/index.js'),//入口文件
    output: {
        path: path.resolve(__dirname, 'build'),//输出路径
        filename: 'bundle.js' //输出文件名
    },
};
```

### 1.8 修改 `package.json`
```diff
  "scripts": {
+    "build": "webpack"
  }
```

### 1.9 执行命令进行编译
```javascript
$ npm run build
```


## 2. loader
使用`babel-loader`来解析`es6写成的模块`
### 2.1 安装loader
```sh
$ npm install babel-loader babel-core babel-preset-es2015 babel-preset-stage-0 --save-dev
```

### 2.2 修改`webpack.config.js`
```diff
module.exports = {
    entry: path.resolve(__dirname, 'src/index.js'),//入口文件
    output: {
        path: path.resolve(__dirname, 'build'),//输出路径
        filename: 'bundle.js' //输出文件名
    },
+    module: {
+        loaders: [
+            {
+                test: /\.js$/,
+                loader: 'babel-loader'
+            }
+        ]
+    }
};
```

### 2.3 修改`src/component.js`
```diff
-  exports.name = 'zfpx';
+  export default name = 'zfpx';
```

### 2.4 修改`src/index.js`
```diff
-  var comp = require('./component.js');
-  console.log(comp.name);
+  import comp from './component.js';
+  console.log(comp);
```

### 2.5 增加`.babelrc`文件
```diff
{
  "presets": ["es2015", "stage-0"],
  "plugins": []
}
```

### 2.6 执行命令进行编译
```javascript
$ npm run build
```

## 3. devServer
`webpack-dev-server`是一个服务器,可以实现项目的预览

### 3.1 安装devServer
```javascript
$ npm install webpack-dev-server --save-dev
```

### 3.3 修改`package.json`
```diff
"scripts": {
+    "dev": "webpack-dev-server"
}

```

### 3.4 配置`webpack.config.js`
```diff
+    devServer: {
+        publicPath: "/static/",//设置在html页面中访问产出文件的路径前缀
+        stats: { colors: true }, //显示颜色
+        port: 8080,//端口
+        contentBase: 'build',//指定静态文件的根目录
+    }
```

### 3.5 修改`build/index.html`
```diff
-  <script src="bundle.js"></script>
+  <script src="/static/bundle.js"></script>
```

### 3.6 执行命令进行启动服务
```javascript
$ npm run dev
```

> 启动此服务的时候，编译后的产出文件放在内存里,在`build`目录下看不见

### 3.7 预览项目 
打开浏览器中访问
[http://localhost:8080](http://localhost:8080)

### 3.8 proxy模拟后台接口
#### 3.8.1 修改配置`webpack.config.js`
```diff
+ function rewriteUrl(replacePath) {//重写url
+     return function (req, opt) {
+         var queryIndex = req.url.indexOf('?');//取得?所在的索引
+         var query = queryIndex >= 0 ? req.url.substr(queryIndex) : "";//取得查询字符串的内容
+         //把proxy的path替换为 '/$1\.json',$1取自path匹配到的真实路径中的第一个分组
+         req.url = req.path.replace(opt.path, replacePath) + query;
+     };
+ }

    devServer: {
        publicPath: "/static/",//设置在html页面中访问产出文件的路径前缀
        stats: { colors: true }, //显示颜色
        port: 8080,//端口
        contentBase: 'build',//指定静态文件的根目录
+       proxy: [
+           {
+               path: /^\/api\/(.*)/,             //替换符合此正则的接口路径
+               target: "http://localhost:8080/", //目标域名端口
+               rewrite: rewriteUrl('/$1\.json'), //重新定向到新的地址,$1取自path正则匹配到的真实路径的第一个分组
+               changeOrigin: true                //修改来源地址
+           }
+       ]
    }
```

#### 3.8.2 在build目录下添加 `book.json`
```
{"name":"javascript"}
```

#### 3.8.3 直接访问此接口
在浏览器输入此地址
[http://localhost:8080/api/book](http://localhost:8080/api/book)
将会被重写向到
[http://localhost:8080/book.json](http://localhost:8080/book.json)


## 4. resolve解析

### 4.1 extension
指定`extension`之后可以不用在`require`或是`import`的时候加文件扩展名,会依次尝试添加扩展名进行匹配

#### 4.1.1 修改 `webpack.config.js`
```diff
+ resolve: {
+   extensions: ["",".js",".css",".json"],
+ },
```

#### 4.1.2  修改`src/index.js`
```diff
- import comp from './component.js';
+ import comp from './component';
```

### 4.2 alias
配置别名可以加快`webpack`查找模块的速度
- 每当引入`jquery`模块的时候，它会直接引入`jqueryPath`,而不需要从`node_modules`文件夹中按模块的查找规则查找
- 不需要`webpack`去解析`jquery.js`文件

#### 4.2.1 先安装jquery
```shell
$ npm install jquery --save
```

#### 4.2.2 修改 `webpack.config.js`

```diff
+ var jqueryPath = path.join(__dirname, "./node_modules/jquery/dist/jquery.js");

resolve: {
       extensions: ["",".js",".css",".json"],
+        alias: {
+            'jquery': jqueryPath
+        }
    },
    
module: {
       loaders: [
           {
               test: /\.js$/,
               loader: 'babel-loader'
           }
       ],
+       noParse: [jqueryPath]
},
```


#### 4.2.3 修改 `build/index.html`
```diff
+ <div id="app"></div>
```


#### 4.2.4 修改 `src/index.js`
```diff
+import $ from 'jquery'
- document.write(comp);
+ $('#app').html(comp);
```


## 5. 解析less样式文件

### 5.1 安装loader
```
$ npm install style-loader css-loader less-loader --save-dev
```

### 5.2 修改配置文件`webpack.config.js`
```diff
+  {
+        test: /\.less/,
+        loader: 'style!css!less'
+  }
```

### 5.3 增加less文件 `src/index.less`
```diff
@color: red;
.red {
  color: @color;
}

```

### 5.4 在`src/index.js`中引入'less'文件
```diff
+  require('./index.less');
```

### 5.5 修改`build/index.html`,使用`red`
```diff
- <div id="app"></div>
+ <div id="app" class="red"></div>
```


## 5. 资源文件的加载
实现CSS、图标、图片等资源文件加载 
### 5.1 安装bootstrap和相应的loader
```
$ npm install bootstrap --save
$ npm install file-loader url-loader --save-de
```


### 5.2 修改 `webpack.config.js`
设置css文件和图标文件的加载器
```diff
 devServer: {
-        publicPath: "/static/",//此行必须删除，不然会找不到图标和图片
        stats: {colors: true}, //显示颜色
        
+ {
+     test: /\.css/,
+     loader: 'style!css'
+ },
+ {
+      test: /\.(woff|woff2|ttf|svg|eot)(\?v=\d+\.\d+\.\d+)?$/,
+      loader: "url?limit=10000"
+ },
+ {
+       test: /\.(jpg|png)$/,
+       loader: "url?limit=8192"
+  }
```

### 5.3 修改 `src/index.js`
```diff
+  import 'bootstrap/dist/css/bootstrap.css';
+  var img = document.createElement("img");
+  img.className = 'img-circle';
+  img.src = require("./zfpx.jpg");
+  document.body.appendChild(img);
```

### 5.4 修改 `build/index.html`
```diff
+ <button class="btn btn-success">bootstrap按钮</button> 
+ <span class="glyphicon glyphicon-qrcode"></span>
+ <img src="/zfpx.jpg" class="img-rounded" alt="lufy">
```


## 6. 自动刷新
在源码修改之后可以自动刷新页面

