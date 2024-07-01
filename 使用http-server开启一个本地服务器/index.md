# 使用http Server开启一个本地服务器


在写前端页面中，经常会在浏览器运行HTML页面，从本地文件夹中直接打开的一般都是file协议，当代码中存在http或https的链接时，HTML页面就无法正常打开，为了解决这种情况，需要在在本地开启一个本地的服务器。
本文是利用node.js中的http-server，开启本地服务，步骤如下：

#### 1.下载node.js
官网地址：[https://nodejs.org](https://nodejs.org)
下载完成后在命令行输入命令`$node -v`以及`$npm -v`检查版本，确认是否安装成功。

#### 2.下载http-server

在终端输入：

`$ npm install http-server -g`

#### 3.开启 http-server服务

终端进入目标文件夹，然后在终端输入：

`$ http-server -c-1`   （⚠️只输入`http-server`的话，更新了代码后，页面不会同步更新）
