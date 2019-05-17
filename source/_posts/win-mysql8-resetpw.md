---
title: Windows MySQL 8.0.11 忘记密码处理
comment: true
date: 2019-05-17 21:14:11
updated: 2019-05-17 21:14:11
categories: MySQL
tags:
---

## MySQL 密码重置思路

MySQL 的密码是存放在 user 表里面的，修改密码其实就是修改表中记录。

重置的思路是想办法不用密码直接进入系统，然后用数据库命令修改表 user 中的密码记录。

MySQL 5 在网上建议的方法大多是以 `--skip-grant-tables` 参数启动 mysql 服务，该参数指示在启动时不加载授权表，因此启动成功后 root 用户可以用空密码登陆

> mysqld –skip-grant-tables

登陆之后可以用

> UPDATE user SET authentication_string='' WHERE user='root';

设置密码或者将密码置空。

**但是，实测该方法在 MySQL 8 中无法成功重置密码**。

## 解决方案

### 方案一：利用 --init-file 参数解决

1. 以管理员身份打开 cmd，停止 MySQL 服务，输入命令

> net stop mysql

2. 在 C:\mysql 目录下（其他路径也行，记得修改下面 init-file 参数后的路径即可）创建 ResetPW.txt 文件，文件内容

> ALTER USER 'root'@'localhost' IDENTIFIED BY 'password';

其中，password 即是新密码

3. 执行 ResetPW.txt 文件

> mysqld --init-file=C:\mysql\ResetPW.txt --console

执行完毕后，打开新的 DOS 窗口

4. 启动 MySQL

> net start mysql

5. 用新密码登录

> mysql -u root -p

### 方案二：想办法使用 --skip-grant-tables 参数

1. 同方案一，以管理员身份打开 cmd，停止 MySQL 服务，输入命令

> net stop mysql

2. 无密码启动

> mysqld --console --skip-grant-tables --shared-memory

3. 打开新的 DOS 窗口，无密码登录

> mysql -u root

4. 清空密码

> UPDATE mysql.user SET authentication_string='' WHERE user='root' and host='localhost';

注意：`authentication_string` 采用的是 plugin 加密方式，故设置为空，不要设置为其他值

5. 打开新的 DOS 窗口，启动 MySQL

> net start mysql

6. 无密码登录
7. 重新设置密码

> alter user root@localhost identified by 'password'

其中，password 即是新密码

8. 新密码登录

> mysql -u root -p