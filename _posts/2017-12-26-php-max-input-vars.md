---
layout: post
title: PHP解决表单过多而丢失的问题
date: 2017-12-26 00:00:00 +08:00
category: blog
tags:  [php]
---

当提交了个很大的表单时（变量内容超过1000个），有些变量服务器可能获取不到，这时候可以在 `php.ini` 文件中修改 `max_input_vars` 的值来解决，默认值是 `1000`

```ini
max_input_vars = 5000
```

PHP在5.3.9以后加入了该特征，这个配置不能动态修改，只能通过 `php.ini`, `.htaccess`, `httpd.conf` 或者 `.user.ini` 修改。
