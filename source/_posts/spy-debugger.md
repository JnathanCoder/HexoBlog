---
title: 移动端真机调试 spy-debugger
date: 2019-07-15 16:53:26
tags:
---

### 关于spy-debugger

一站式页面调试、抓包工具。远程调试任何手机浏览器页面，任何手机移动端`webview`（如：微信，HybridApp等）。支持H`TTP/HTTPS`，无需USB连接设备。

### 特性

1、页面调试＋抓包
2、操作简单，无需USB连接设备
3、支持HTTPS。
4、spy-debugger内部集成了weinre、node-mitmproxy、AnyProxy。
5、自动忽略原生App发起的https请求，只拦截webview发起的https请求。对使用了SSL pinning技术的原生App不造成任何影响。
6、可以配合其它代理工具一起使用(默认使用AnyProxy) (设置外部代理)

<!-- More -->

### 安装

```bash
[sudo] npm install spy-debugger -g
```

### 三分钟上手

第一步：手机和PC保持在同一网络下（比如同时连到一个Wi-Fi下）
第二步：命令行输入spy-debugger，按命令行提示用浏览器打开相应地址。
第三步：设置手机的HTTP代理，代理IP地址设置为PC的IP地址，端口为spy-debugger的启动端口(默认端口：9888)。

Android设置代理步骤：设置 - WLAN - 长按选中网络 - 修改网络 - 高级 - 代理设置 - 手动
iOS设置代理步骤：设置 - 无线局域网 - 选中网络 - HTTP代理手动

第四步：手机安装证书。

`spy-debugger initCA` 会将证书放置到系统根目录node-mitmproxy文件夹。
Mac	如：/users/yourName/node-mitmproxy
Windows 如：C/users/yourName/node-mitmproxy
将文件夹下的node-mitmproxy.ca.crt到手机上安装

第五步：用手机浏览器访问你要调试的页面即可。

### 效果截图
![PC端调试界面](/assets/img/spy-debugger.jpg)
![移动端界面](/assets/img/mobile-effect-snap.jpeg)


### 自定义选项

#### 端口(默认端口9888)

```js
spy-debugger -p 9999
```

#### 设置外部代理（默认使用AnyProxy）

```js
spy-debugger -e http://127.0.0.1:8888
```

#### 设置页面内容为可编辑模式（默认false）

```js
spy-debugger -w true
```

该功能使页面内容修改更加直观方便。内部实现原理：在需要调试的页面内注入代码：document.body.contentEditable=true。暂不支持使用了iscroll框架的页面。

#### 是否允许weinre监控iframe加载的页面(默认false)

```js
spy-debugger -i true
```

#### 是否只拦截浏览器发起的https请求（默认true）

```js
spy-debugger -b false
```

#### 是否允许HTTP缓存(默认false)

```js
spy-debugger -c true
```

