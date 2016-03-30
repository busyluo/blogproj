---
title: 使用Nginx 转发 个人域名到csdn
date: 2016-03-30 13:09:54
category: 折腾
tags: [Nginx,域名,VPS]
---

去年申请了个域名，一直没用上，又没有时间自己搭建网页，于是想着直接转发到CSDN上，好像是一个不错的主意。完成后，可以通过 csdn.lxf.pw 访问到本博客。

#准备工作
 1. 申请一个域名：我的是在西部数码（west.cn）上弄的，一年也就几十块钱。
 2. 搭建一个VPS: 我的是在搬瓦工（bandwagonhost.com）买的，应该是最便宜的，不过好像限量，最便宜的不一定能买到。这个除了用来当服务器，还可以同时用Shadowsocks搭个梯子，愉快的上谷歌，上面有一键搭建的功能，很是方便。
 （后文会以我选择的方案为例子，其它的方案应该也只是大同小异）

#开始
### 配置VPS
- 在后台管理里，Install new OS  里，选择centos-6 x86，等待安装完成。
- 记下VPS的IP地址

### 添加域名解析
在west.cn的管理中心->我的域名里，选择域名解析，将VPS的IP地址填进去并点击添加，如下图
![域名解析示例](http://img.blog.csdn.net/20160312121229396)
	
	
###配置Nginx服务器
选择VPS后台管理里的Root shell - interactive 打开一个SSH，按步骤输入指令。
1.首先安装Nginx：
```
yum install epel-release
yum install nginx
```
中间可能会提示你输入yes或者no， 输入yes即可。

2.安装完成后，配置Nginx：
打开Nginx配置文件   
```
vi /etc/nginx/nginx.conf
```
通过方向键找到 http { } 这个段，并修改成下面这个样子：

```
http {

	（这是http里原来的内容）
	
	 server {
        server_name csdn.lxf.pw;
        return 301 $scheme://blog.csdn.net/busyluo$request_uri;
     ｝
}
```
修改时，先将光标移动到server的位置，按i进入INSERT模式，将csdn.lxf.pw换成自己的域名，将busyluo换成的用户名。输入完成后，按ESC，再输入以下内容：
```
:wq
```
以上这条指令代表保存并退出。
修改完成后，重启Nginx：
```
nginx -s reload
```
如果提示错误，检查一下配置文件，再重新输入上面这条指令。

至此，设置就完成了，访问一下试试吧。
