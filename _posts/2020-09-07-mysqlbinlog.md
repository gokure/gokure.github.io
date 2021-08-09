---
layout: post
title: 从Binlog中提取指定日期的SQL
date: 2020-09-07 00:00:00 +08:00
category: blog
tags:  [mysql, binlog]
---

首先在MySQL中使用 `show master logs` 列出binlog日志列表

```bash
mysql> show master logs;
+------------------+------------+
| Log_name         | File_size  |
+------------------+------------+
| mysql-bin.000011 | 1073744535 |
| mysql-bin.000012 | 1074119618 |
| mysql-bin.000013 | 1074007107 |
| mysql-bin.000014 | 1073742108 |
| mysql-bin.000015 |  323415006 |
| mysql-bin.000016 |  367284238 |
+------------------+------------+
6 rows in set (0.00 sec)
```

然后使用`mysqlbinlog`命令导出指定日期的SQL

```bash
sudo mysqlbinlog --base64-output=decode-rows -v --start-datetime='2018-01-25 14:00:00' --stop-datetime='2018-01-25 15:00:00' /var/lib/mysql/mysql-bin.000010 > dump.sql
```
