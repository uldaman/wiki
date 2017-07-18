---
title: NodeJs 随笔
layout: page
date: 2016-12-12 18:13
---

https://nodejs.org/api/

回调
事件触发回调

让 JavaScripte 运行在服务端的开发平台

异步式 I/O 与事件驱动的架构设计 (传统是多线程模型)

这种方式充分利用 CPU 资源, 因为线程不会被阻塞, 而多线程的话, 如果遇到 IO 操作, 发生 IO 操作的线程是会被阻塞的, 如果有新任务又会新开线程...<br>
这种异步的方式, 并不是说只能有一个线程, 也是可以创建多线程的, 它可以将单线程绑定到单个 CPU, 充分利用 CPU 资源.

console.log("你好")
你好  -- 输出
undefined -- 返回值

node -v 版本
node -e "xxx" eval ecript 解析字符串

创建一个 server:

```js
"use strict";

var http = require("http");

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    res.write('<h1>Node.js 已经运行了服务</h1>');
    res.end('<p>视频出外: PCAT</p>');
}).listen(5586);

console.log("http://127.0.0.1:5586");
```
<br>

调试代码: npm install -g supervisor
使用 supervisor xxx.js 启动

```
supervisor sss.js

Running node-supervisor with
  program 'sss.js'
  --watch '.'
  --extensions 'node,js'
  --exec 'node'

Starting child process with 'node sss.js'
Watching directory 'C:\Users\chaoqun.zhu\Desktop\testjs' for changes.
Press rs for restarting the process.
http://127.0.0.1:5586
crashing child
Starting child process with 'node sss.js'
http://127.0.0.1:5586
crashing child
Starting child process with 'node sss.js'
http://127.0.0.1:5586
crashing child
Starting child process with 'node sss.js'
http://127.0.0.1:5586
```
<br>

异步读文件

```js
var fs = require('fs');
fs.readFile('file.txt', 'utf8', function(err, data) {
    if (err) {
        console.log(err);
    } else {
        console.log(data);
    }
});
console.log('End . . .');
```
<br>
同步读文件

```js
var fs = require('fs');
var data = fs.readFileSync('file.txt', 'utf8');
console.log(data);
console.log('End . . .');
```
<br>
关键就在于异步是通过回调的机制, NodeJs 会响应 I/O 事件.

自定义事件

```js
// 声明事件对象
var events = require('events');
var eventEmitter = new events.EventEmitter();

// 注册事件
eventEmitter.on('some_event', function() {
    console.log('事件触发成功.');
});

// 触发事件
setTimeout(function() {
    event.emit('some_event');
}, 1000);

console.log('程序执行完毕.');
```
<br>
输出结果:

```
程序执行完毕.
事件触发成功.
```
<br>
可以看到事件是异步的, 先打印 `程序执行完毕.` 再打印 `事件触发成功.`.

模块, 一个 Node.js 文件就是一个模块

require 导入其他模块
exports 导出模块

暴露接口 set_name.js:

```js
var g_name;

exports.setName = function(_name) {
    g_name = _name;
}
```
<br>
使用接口:

```js
var test = require('./set_name.js');
test.setName('金三顺';)
```
<br>
还能定义可调用模块, 类似 python 中的 `__call__`

暴露接口 set_name.js:

```js
var g_name;

module.exports = function(_name) {
    g_name = _name;
}
```
<br>
使用接口:

```js
var test = require('./set_name.js');
test('金三顺';)
```
<br>
还可以把对象封装到模块中.

暴露接口 set_name.js:

```js
module.exports = function() {
    var m_name;
    this.setName = function(_name) {
        m_name = _name;
    };

    this.sayHello = function() {
        console.log('Hello ' + m_name);
    };
};
```
<br>
使用接口:

```js
var test = require('./1.js');
var hello = new test();
hello.setName('11');
hello.sayHello();
```
<br>
这就有点像面向对象的类概念了.

包, 概念上和 Java 的包差不多, 就是将功能相关的模块打包.

包规范
package.json 必须在顶层目录
二进制文件应该在 bin 目录
js 文件应该在 lib 目录
文档应该在 doc 目录
单元测试应该在 test 目录


通过定制 package.json 可以创建更复杂、更完善、更符合规范的包.
Node.js 在调用包时, 首先会检查包中的 package.json 文件的 main 字段, 将其作为包的接口模块, 如果 package.json 文件的 main 字段不存在, 那么 Node.js 会尝试寻找 index.js 或 index.node 作为包的接口.

package.json 文件是 CommonJS 规范用于描述包的文件, 完全符合规范的 package.json 文件应该包含以下字段:

- name: 包名. 包名是唯一的, 由小写字母、数字和下划线组成, 不能含空格.
- description: 包说明. 对包进行简要描述.
- version: 版本号. 满足《语义化版本识别》规范的版本字符串.
- keywords: 关键字数组, 通常用于搜索.
- maintainers: 维护者数组. 每个元素包含 name、email(可选)、web(可选) 字段.
- contributors: 贡献者数组. 格式与maintainer数组相同.包作者应该是贡献者数组的第一个元素.
- bugs: 提交bug的地址, 可以是网址或电邮地址.
- licenses: 许可证数组. 每个元素要包含type(许可证名称)和url(链接到许可证文本的地址)字段.
- repositories: 仓库托管地址数组. 每个元素要包含type(仓库的类型, 如Git)、url(仓库地址)和path(相对于仓库的路径, 可选)字段.
- dependencies: 包依赖. 是一个关联数组, 由包名和版本号组成.

包管理器 npm


本地安装: 将模块下载到当前命令行所在目录的 node\_modules 中, 可在当前包中 require
全局安装: 安装到系统的 node\_modules, windows 默认是 `用户/AppData/Roaming/npm/node_modules`, 不能在当前包中 require, 但可在命令行下直接运行


npm config get prefix 来获取当前设置的全局目录
npm config set prefix 设置全局目录


初始化包
npm init

安装
npm install/i \[-g\] package_name

卸载
npm uninstall package_name \[-g\]

查看当前包
npm list \[-g\] --depth 0

查看某个模块
npm view package_name

代码调试
http://www.cnblogs.com/moonz-wu/archive/2012/01/15/2322120.html
http://www.cnblogs.com/kumbayaco/p/3439142.html

JavaScript 中有一个特殊的对象, 称为全局对象 (Global Object), 它及其所有属性都可以在程序的任何地方访问, 即全局变量.

在浏览器 JavaScript 中, 通常 window 是全局对象,  而 Node.js 中的全局对象是 global, 所有全局变量 (除了 global 本身以外) 都是 global 对象的属性.

在 Node.js 我们可以直接访问到 global 的属性, 而不需要在应用中包含它.

global 最根本的作用是作为全局变量的宿主. 按照 ECMAScript 的定义, 满足以下条 件的变量是全局变量:

- 在最外层定义的变量 (永远不可能成立, 看下面说明)
- 全局对象的属性
- 隐式定义的变量 (未定义直接赋值的变量)

当你定义一个全局变量时, 这个变量同时也会成为全局对象的属性, 反之亦然;

需要**注意**的是, 在 Node.js 中你**不可能**在最外层定义变量, 因为所有用户代码都是属于当前模块的,  而模块本身不是最外层上下文.

**注意**: 永远使用 var 定义变量以避免引入全局变量, 因为全局变量会污染命名空间, 提高代码的耦合风险.

process.nextTick(callback) 将一个耗时的操作放到队列时, 下次执行.

console.log();
console.error();
console.trace(); 输出当前的调用栈

util 全局变量
util.inherits(child, TestParent)
对象间原型继承

js 中, 是没有类 (Class) 关键字的, 都用 function, 但 js 严格模式中, 通过函数名的大小写来规定是类还是函数, 如下面代码:

```js
function testParent() {
    this.name = 'test';  // 会报提示 Possible strict violation
}
```
<br>
js 规范认为小写字母开头是方法, 所以是没有 this 的, 改成大写字母开头就好了:

```js
function TestParent() {
    this.name = 'test';  // 不报错
}
```
<br>
js 好像没有我理解的传统意义上的继承, 但它通过 util.inherits(child, TestParent) 可以实现原型链继承, 原型链是对象的 prototype 属性, 如:

```js
"use strict";

var util = require('util');

function TestParent() {

}

TestParent.prototype.test_out = function(){
    console.log('继承原型函数');
};  // 原型

function TestChild() {

}

util.inherits(TestChild, TestParent);

var child = new TestChild();

child.test_out();

```
<br>
冒号的作用

js 中, 冒号可以用来声明直接量对象的成员 (直接量 我理解就是 var 定义的变量), 如:

```js
var test_colon = {
    test_val : 'value1',
    test_func : function() {
        consol.log('this is test.');
    },
};
```
<br>
`util.inspect(object, [showHidden, [depth], [colors]])` 将任意对象转换成字符串.

showHidden true 会输出更多隐藏信息
depth 最大递归对象层数
colors 染色

events 是 node.js 最重要的模块, 没有之一.
events.EventEmitter 事件发射与监听的封装

EventEmitter 有个特殊事件  error, 它包含了错误的定义, 程序异常通常就会发射 error 事件, 如果没有相应的处理器, node.js 会把它当作异常处理, 退出程序并打印调用栈.

fs 模块是文件操作的封装
fs 的所有操作都提供了同步和异步两个版本.

fs.open fs.close 要对应
fs.openSync fs.closeSync

http 模块, 封装了一个高效的 http 服务器, 和一个简易的 http 客户端
http.server 基于事件的 HTTP 服务器.
http.request 则是 HTTP 客户端工具, 向服务器发送请求.

http.creaeServer 创建一个 http.server 的实例

http.server 基于事件, 所有请求都被封装到独立的事件.

request 当客户端请求到来时事件, 参数: req, res, 分别是 http.ServerRequest (现在好像是 http.IncomingMessage) 和 http.ServerResponse 的实例.

connection 当 tcp 连接建立时事件, 提供参数 socket, 是 net.Socket 的实例

close 服务器关闭时事件

还有 ckeckContiune, upgrade, clientErrot, 等事件

http 提供一个绑定 request 事件的捷径, 即 http.creaeServer 时就会绑定, 它相当于:

```js
var http = require('http');
var server = new http.Server();
server.on('request', function(req, res) {
    res.end('响应.');
});
```
<br>
http.IncomingMessage 主要属性:
method 是 post 还是 get
url 请求地址, 通过以下方法可以解析 url 地址:

`require('querystring').parse(url)` or `require('url').parse(url, true)`.

```js
require('url').parse('/status?name=ryan', true)
{
  href: '/status?name=ryan',
  search: '?name=ryan',
  query: {name: 'ryan'},
  pathname: '/status'
}
```
<br>
通过 http.IncomingMessage 获取不取 post 的参数, node.js 将请求头和请求体分开处理了..
所以如果是 post 的话, 需要单独处理了:

首先要知道请求体的三个事件:

data 事件, 当请求体到来时触发
end 事件, 数据传输完成时触发
close 事件, 客户请求结果时触发, 包括用户强制终止

```js
/**
 * 因为 post 方式的数据不太一样可能很庞大复杂，
 * 所以要添加监听来获取传递的数据
 * 也可写作 req.on('data',function(data){});
 */
 var qs = require('querystring');
 var util = require('util');

 var postData = '';

req.addListener('data', function (data) {
    postData += data;
});

/**
 * 这个是如果数据读取完毕就会执行的监听方法
 */
req.addListener('end', function () {
    var query = qs.parse(postData);
    res.end(util.inspect(query));
});
```
<br>

http.ServerResponse 响应对象
`writeHead(statusCode, [headers])`
`write(deta, [encoding])`
`end([data], [encoding])` 必须调用


http 客户端, 提供两个函数, http.request, http.get, 向 http 发起请求.

http.request 可以指定 method, http.get 是对 http.request(method=get) 的封装.

要注意一下, path 参数要包含 '/' 根路径, 如 '/search?query=martin'.

这两个函数返回 http.ClientRequest 实例.

```js
var postData = querystring.stringify({
  'msg' : 'Hello World!'
});

var options = {
  hostname: 'www.google.com',
  port: 80,
  path: '/upload',
  method: 'POST',
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded',
    'Content-Length': Buffer.byteLength(postData)
  }
};

var req = http.request(options, (res) => {
  console.log(`STATUS: ${res.statusCode}`);
  console.log(`HEADERS: ${JSON.stringify(res.headers)}`);
  res.setEncoding('utf8');
  res.on('data', (chunk) => {
    console.log(`BODY: ${chunk}`);
  });
  res.on('end', () => {
    console.log('No more data in response.');
  });
});

req.on('error', (e) => {
  console.log(`problem with request: ${e.message}`);
});

// write data to request body
req.write(postData);
req.end();
```
<br>

http.ClientRequest, 表示一个已经产生而且正在进行的 http 请求.
`request.write(chunk[, encoding][, callback])`
请求必须调用 `request.end([data][, encoding][, callback])` 方法显示地结束请求.
它还有个常用的方法 `request.setTimeout(timeout[, callback])`

http.ClientResponse (好像也没了, 改成 http.IncomingMessage, 即 ServerRequest 和 ClientResponse 都是 http.IncomingMessage, 它是 stream.Readable 的实现, 都具有 data, end, close 等事件)

还有个常用的方法 res.pause() 暂停接受数据和发送事件, 主要用来实现下载功能. res.resume() 恢复传输.

Express
npm install -g express
npm install -g express-generator
express -V

express -e 项目名 (-e 使用 ejs engine, 默认 jade)
cd 项目名 && npm install
SET DEBUG=项目名:* && npm start


使用工具生成 express-generator

手动生成:
app.js
cd myapp %% npm install express

```js
var http = require('http');
var express = require('express');
var app = express();
http.creareServer(app);
app.get('/demo', function(req, res) {
    res.send('hello server.');
    res.end();
});
app.listern(3000);
```
<br>
Express 中间件
http://blog.fens.me/nodejs-connect/
http://www.expressjs.com.cn/resources/middleware.html
http://www.expressjs.com.cn/guide//routing.html

(前 Connect)
包装 request response
body-parser POST 请求体解析
query Get 请求体解析
static 静态资源
cookie
cookieSession
logger

__dirname__ 当前 js 目录

自定义中间件, 就是个匿名函数
req, res, next
next() -- 调用下一个中间件
