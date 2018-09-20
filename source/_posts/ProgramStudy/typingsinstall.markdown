---
layout: "post"
title: "Angular2项目demo——安装"
date: "2016-11-16 11:11"
tags:
- Angular2
- Typings
- nodeJs
categories:
- study
- fe
---

好友最近在学Angular2，刚完成一个feature，还提交到GIT上。我心下好奇，就准备玩一下他的这个小功能。谁知，卡在工程部署上。

[Frey-Fu][e43976ac]

  [e43976ac]: https://github.com/Frey-Fu/ng2-echarts "Frey-Fu's ng2-echars"


## 部署过程
### clone项目到本地
  ```bash
git clone https://github.com/Frey-Fu/ng2-echarts.git
  ```

### 阅读README.md

> This is ECharts for Angular2, code is in src/, there's an example project in example/, feel free to use it. The example is based on angular2-webpack-starter.

#### How to use:
```
cd example
npm install
typings install
npm run build:prod
npm run server:prod
```

### 安装typings

查看官方手册：
https://www.npmjs.com/package/typings

#### Install Typings CLI utility.
``` bash
npm install typings --global
```
结果好像成功了：
```
typings@2.0.0 node_modules/typings
├── elegant-spinner@1.0.1
├── has-unicode@2.0.1
├── listify@1.0.0
...
...

```

### 测试Typings,报错

```
# Search for definitions.
typings search tape
```

结果：

```
/usr/local/lib/node_modules/typings/node_modules/typings-core/node_modules/strip-bom/index.js:2

module.exports = x => {
                    ^
SyntaxError: Unexpected token >
    at Module._compile (module.js:439:25)
    at Object.Module._extensions..j
...
...

```
### Review typings的安装日志，发现一些警告

我的node版本低于要求
```
...
npm http 304 https://registry.npm.taobao.org/is-utf8
npm WARN engine deep-extend@0.4.1: wanted: {"node":">=0.12.0","iojs":">=1.0.0"} (current: {"node":"v0.10.24","npm":"1.3.21"})
npm http GET https://registry.npm.taobao.org/wrappy
npm http GET
...
```

## 升级node

http://theholmesoffice.com/node-js-fundamentals-how-to-upgrade-the-node-js-version/

1: Check your current version of Node.

```
$node -v
```
2: Clear your npm cache
```
sudo npm cache clean -f  
```

3: Install ‘n’
```
sudo npm install -g n
```

4: Upgrade to a stable version
```
sudo n stable  

```
5: Check the running version of Node to verify that it has worked:
```
$node -v v0.8.11
```

## 重装node

参考1： http://linyehui.me/2016/03/03/reinstall-nodejs-on-osx.html

参考2： http://icarus4.logdown.com/posts/175092-nodejs-installation-guide

### 再安装

```


$ typings install


...
npm WARN webpack-dev-server@2.1.0-beta.11 requires a peer of webpack@^2.1.0-beta.26 but none was installed.
jingchen@chentekiair ~/work/gitlab/fuwei/ng2-echarts/example $ typings install

└── echarts (global)
```

### 编译运行
```
npm run build:prod
npm run server:prod
```

服务启动成功，结果如下：

```

> angular2-webpack-starter@5.0.5 server:prod /Users/jingchen/work/gitlab/fuwei/ng2-echarts/example
> http-server dist --cors

Starting up http-server, serving dist
Available on:
  http://127.0.0.1:8080
```

### 打开页面

![fuwei_ng_echarts_demo](http://ogqir9ige.bkt.clouddn.com/826660e135fa7da8393a25133803a9da.png)
