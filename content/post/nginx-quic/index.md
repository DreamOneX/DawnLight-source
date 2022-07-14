---
title: "nginx-quic部署笔记"
date: 2022-07-14T21:48:18+08:00
draft: false
slug: "nginx-quic"
image: "internet.png"
categories:
    - code
tags:
    - nginx
    - linux
    - web
description: "nginx是个很著名的http服务器，nginx-quic分支使它支持http/3的分支之一（来自nginx官方）了解quic后，迫不及待（划掉）地想搞一个玩玩，但踩了N个坑，所以留个笔记（顺便证明一下我的博客还活着）"
---

其实，编译安装nginx-quic的教程很多，但关于nginx配置有的过时（写于**2022-07-14**，~~俺也不能保证我这个不会过时对吧~~），要么~~很难找到~~（事实上我就没找到）  
# ~~编译~~安装  
```shell
paru -S nginx-quic  
```  
~~ok了，Arch真好~~  
# 配置  
`http/3`使用udp，所以需要额外添加一个listen语句  
```nginx
listen 443 ssl http2;
listen 443 http3 reuseport;
```  
**IMPORTANT**: 一个端口只需要写一次`reuseport`
举个例子:
```nginx
...
server {
    ...
    listen 443 http3 reuseport;
    ...
}
server {
    ...
    listen 443 http3;
    ...
}
...
```
# ~~踩雷时间到~~  
### 1. 防火墙未放行 `443/udp`  
> ~~激动的心，颤抖的手~~，但是，忘放行了

```shell
sudo firewall-cmd --zone=public --add-port=443/udp
sudo firewall-cmd --zone=public --add-port=443/udp --permanent
```  
### 2. Alt-Svc标头和QUIC-Status标头  
> 当时按照网上的教程，抄了一个Alt-Svc，用curl检测时，无法发起http/3请求，查了很久找不到资料，然后 ~~灵机一动~~ 找了个支持http/3的网站抄了一个   

部分教程`Alt-Svc`标头过期，导致无法发起`http/3`请求  
```nginx
add_header Alt-Svc 'h3=":443"; ma=86400';
add_header QUIC-Status $http3;
```  
### 3. `ssl_stapling ignored`
> ~~特大号巨坑，全网没几篇文章提到~~，手搓了半天证书，一直没起作用，搞了好久才发现nginx-quic不支持OCSP而不是证书有问题，另外，实际使用中差别并不大，如果有需要可以用OpenSSL编译nginx-quic    

nginx-quic使用`BoringSSL`，但是nginx的ocsp实现完全基于`OpenSSL`，所以`sudo nginx -t`会提示：    
```shell
"ssl_stapling" ignored, not supported
```   
~~没特殊需要，不必管它~~  
 
---

# 最后  
#### 检测HTTP/3支持  
* [http3check](https://http3check.net/)  
#### nginx配置文件生成  
* [NGINXConfig | DigitalOcean](https://www.digitalocean.com/community/tools/nginx)  
