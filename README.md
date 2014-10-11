chat-socket.io
==============

<h1>前端抢后端饭碗 -- Node.js + Socket.io 制作简易聊天室</h1>

<author>
<a href="http://leehey.org/homepage" target='_blank'>LeeHey 个人主页</a>
</author>

<h3>1. 前言</h3>

<p>看到这个题目的时候干后端的别打我。在接触Socket.io之前曾经用PHP + jQuery写了一个低效的长轮询只有消息同步功能的小聊天室就已经耗尽心力，更不用说利用PHP的Socket接口写WebSocket的聊天室，那更是灾难。</p>

<p>刚才一口气说了一堆大家都困惑的术语，接下来等本菜鸟解释一下。</p>

<p>一般来说，浏览器在接收到客户端请求(request)的时候，会发送给服务器端进行处理，服务器端处理完毕后会返回结果(response), 这样就完整地完成了一次HTTP的请求。当客户端想再更新页面信息的时候，需要刷新浏览器，再完成一次HTTP请求。</p>

<p>后来， AJAX的流行貌似使网站更加用户友好，增强了互动性，但实际上在效率上并没有提高，AJAX与后台的交互实际也是在一次又一次地完成HTTP请求。终于， 人们为了更好地实现实时应用，想出了一些办法，老办法叫Comet, 新办法叫Websocket.</p>

<p>老办法Comet是改良自AJAX（轮询）。拿PHP作为例子，其原理就是，利用AJAX给PHP后台发送请求，后台PHP设置运行时间为无限，并用一个死循环使HTTP请求不返回而达到持续监听后台的效果。一旦死循环里的代码检测发现数据/文件被修改，则跳出循环，返回更新后的数据。这种办法简明易懂，后台代码不需要写太多，但因为连接不断开而大量服务器资源。</p>

<p>新办法就是HTML5新的API叫做WebSocket.WebSocket采用了一些特殊的报头(Header)，使得浏览器和服务器只需要做一个握手的动作，就可以在浏览器和服务器之间建立一条连接通道，而毋须消耗大量服务器资源。更进一步的原理，大家可以参考下面两篇文章:</p>

<p>
(1) <a href="https://github.com/xionglun/goWebBook/blob/master/docs/08.2.md" target="_blank">https://github.com/xionglun/goWebBook/blob/master/docs/08.2.md</a>
</p>

<p>
(2) <a href="http://www.html5rocks.com/zh/tutorials/websockets/basics/" target="_blank">http://www.html5rocks.com/zh/tutorials/websockets/basics/</a>
</p>

<p>
今天我们会介绍用Node.js和Socket.io来轻松完成这个任务。
</p>

<p>
Socket.io是使用得非常普遍的前端调用Websocket的库，其<a href="http://socket.io/" target="_blank">官方网站</a>里面还自带了后台实现的代码。我们用Node.js也是奔着这一点去的，因为你们会发其前后端代码居然一模一样，我们毋须再为PHP，JAVA那些繁杂的代码而忧心。
</p>

<h3>2. 基本知识</h3>

<p>
<ul>
	<li>Websocket基础知识</li>
	<li>Javascript</li>
	<li>Node.js</li>
	<li>Express.js（Node.js中最流行的网页框架</li>
</ul>
</p>


<h3>3. 正文</h3>
<p>
	运行环境: Mac OSX 10.9.5
</p>
<p>
	首先我们需要安装Node.js以及其包管理npm。在此不赘述，请参考官方安装指南：
</p>

<p>
	<a href="https://github.com/joyent/node/wiki/Installation">官方官装指南</a>
</p>

<p>
	安装完之后，我们要新建一个文件夹。
</p>
<pre>
mkdir chat
cd chat
</pre>

<p>
	然后我们新建一个/chat/package.json文件，并输入以下代码。这个文件存放Node.js应用的依赖环境及模块。
</p>

<pre>
	{
	  "name": "chat",
	  "version": "0.0.1",
	  "description": "first socket.io app",
	  "dependencies": {
	    "express": "^4.9.6",
	    "socket.io": "^1.1.0"
	  }
	}

</pre>

<p>
	然后输入以下命令，系统就会帮你自动安装所有需要的模块。并在/chat下面多出一个node_modules的文件夹存放所需的模块。
</p>

<pre>
npm install
</pre>

<p>
	/chat/index.js文件，并输入以下代码
</p>

<pre>
/**
 * index.js
 * @author LeeHey 
 * @email lcxfs1991@gmail.com
 * @description
 * This file is to setup the basic Node.js server and require necessary server module
 */

//express.js framework
var express = require('express');
var app = express();

//create an http object based on express.js
var http = require('http').Server(app);

//module manage folder path
var path = require('path');

//main processing file
var chat = require('./routes/chat');

//set /public as the folder to serve static files
app.use(express.static(path.join(__dirname, 'public')));

//route / to /public/index.html
app.get('/', function(req, res){
  res.sendFile('./public/index.html');
});


//initialize all http setting in chat object
chat.initialize(http);


//listen to port 2501
http.listen(2501, function(){
  console.log('listening on *:2501');
});
</pre>









