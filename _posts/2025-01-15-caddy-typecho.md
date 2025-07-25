---
layout: post
title: "Caddy 配置 Typecho — 再访"
date: 2025-01-15 00:00:00 +0800
categories: [web]
tags: [caddy, php, typecho]
---

在这篇文章中我记录了如何使用 **Caddy** 和 **PHP** 部署 Typecho
博客系统的完整过程。 这是对我早期笔记的一次更新，以更精美的
步骤和新的版本号替代了过时的信息。

## 安装 PHP

首先需要在服务器上安装 PHP 8.2 及其常用扩展，例如 `php8.2-fpm`
和 `php8.2-mysql`。 安装完成后编辑 `/etc/php/8.2/fpm/php.ini`
以设置正确的时区（例如 `date.timezone = Asia/Taipei`）并根据需要
调整 `memory_limit` 等参数。

## 安装 Caddy

接下来通过官方仓库安装 Caddy v2，Caddy 是一款现代的 Web 服务器
和反向代理。 安装完成后，创建一个 `Caddyfile` 来配置站点。 例如：

```caddyfile
example.com {
    encode gzip
    root * /var/www/typecho
    php_fastcgi unix//run/php/php8.2-fpm.sock
    file_server
}
```

这个配置启用了压缩、设置了站点根目录，使用 Unix 域套接字将 PHP
请求转发给 PHP‑FPM，并启用了静态文件服务。

## 部署 Typecho

最后，下载并解压最新版本的 Typecho 到 `/var/www/typecho`，设置
目录权限，然后重启 Caddy 和 PHP‑FPM。 访问域名即可开始配置
Typecho 博客。 通过这种方式，部署过程简洁可靠，易于维护。
