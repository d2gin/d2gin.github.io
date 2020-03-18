---
title: Apache error 504 Gateway Timeout
date: 2020-03-07 01:51:09
index_img: /cover/43169ac71b42d8414bde4bf57f3e00f6.jpg
banner_img: /cover/43169ac71b42d8414bde4bf57f3e00f6.jpg
tags:
	- Apache
---

## 环境

CentOS 下 Oneinstack 安装 Apache + Mysql + PHP7

## 现象

一个 PHP 脚本需要长时间执行，执行超出 60 秒即返回 Http 504 Gateway Timeout

类似像这样的代码脚本执行，就会报错

```
<?php
echo date('h:i:s') . "<br>";
sleep(120);
echo date('h:i:s');
?>
```

## 解决方案

不知为何，此版本 Oneinstack 安装后，需要在 Httpd.conf 中添加：
`Timeout 600`
600 的单位是秒，根据实际需要设置，设置后重启 Httpd 服务

## 另一个问题

按如上设置后，504 问题解决，产生新的 503 错误

## 解决方案

```
/usr/local/php/conf/etc/php-fpm.conf`
其中的更改为
`request_terminate_timeout = 0
```