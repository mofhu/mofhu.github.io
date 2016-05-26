---
layout: post
title: chaos-to-wordpress
---

## WordPress 开发小记

前一阵莫名躺了个枪, 需要开发一个小站点. 为了后期不会写代码的用户们维护方便,
考虑要有所见即所得的后台界面为好. (Jekyll 虽然好, 静态站点部署也方便得多,
但在用户最大的前提下, 也完全没有列入考虑.)
很自然地选择了成熟的博客后台 WordPress. 于是跳了一些坑, 也填了一些坑, 是为记.

## 本地开发与调试

初期开发测试时, 最好能在本地部署, 以提高`开发-测试-反馈`循环的速度.
在 OS X 上, 可用打包好的 MAMP 来进行本地测试[^1].

## MAMP 迁移到 Wampserver

本地测试基本完成后, 下一步是把整个 WordPress 服务架在服务器上.
手上的机器是 Win 7, 选择了 Wampserver 提供环境, 参考 Windows 下迁移的文章[^2].

主要进行两步操作:

1. 从 MAMP 的 MySQL 导出 WordPress 的所有表到文件, 并导入 Wampserver 的 MySQL.
2. 拷贝 OS X 下的 WordPress 文件夹到 Wampserver.

为使迁移后的博客可以直接在 Windows 下调试, 最好先把 blog 的网址设为
**在两台机器都可使用的地址**.
否则网站迁移过来无法访问的时候, 想改网址也不能登陆后台, 会非常尴尬
(可以改 MySQL 的表中的网址, 但网站内容中的很多链接可能会失效).

## Wampserver 下更改访问网站的权限

迁移后发现服务器本地可正常访问站点, 但其它机器联网访问时遇到没有权限的问题.
Google 发现需要设置访问数据库的权限, 使其它地址也能访问[^3].

~~~
Alias /bs1 "c:/_DEV_/git/NewProject/www/"

<Directory "c:/_DEV_/git/NewProject/www/">
    Options Indexes FollowSymLinks MultiViews
    AllowOverride all
    Require local         <-- to develop on this PC
    Require ip 192.168.1  <-- to access the server from another PC on your network
    Require all granted   <-- to allow the world to see the beauty of your site
</Directory>
~~~


## MySQL 误调 root 权限的恢复

迁移完成之后, 自己挖了一个大坑: 为安全计我想把 MySQL 的 root 用户禁掉.
然而我忘了把 WP 的用户打开对应的权限. 于是, 所有的 MySQL 用户都不能访问表,
网站直接无法访问 :(

求助了万能的 Google, 发现可以通过 `mysqld -nt --skip-grant-tables`,
在启动 MySQL 时不检查用户权限. 再写 SQL 语句修改用户权限表(mysql.user), 即可恢复用户的权限[^4].

~~~sql
UPDATE USER SET select_priv='Y',Insert_priv='Y',Update_priv='Y',Delete_priv='Y',Create_priv='Y',Drop_priv='Y',Reload_priv='Y',File_priv='Y',Grant_priv='Y',References_priv='Y',Index_priv='Y',Alter_priv='Y',Show_db_priv='Y',Super_priv='Y',Create_tmp_table_priv='Y',Lock_tables_priv='Y',Execute_priv='Y',Repl_slave_priv='Y',Repl_client_priv='Y',Create_view_priv='Y',Show_view_priv='Y',Create_routine_priv='Y',Alter_routine_priv='Y',Create_user_priv='Y',Event_priv='Y',Trigger_priv='Y',Create_tablespace_priv='Y' WHERE user='root';
~~~

---

参考资料:

[^1]: [Installing WordPress Locally on Your Mac With MAMP « WordPress Codex](https://codex.wordpress.org/Installing_WordPress_Locally_on_Your_Mac_With_MAMP)
[^2]: [本地安装WordPress调试以及上传到远程服务器 Spark & Shine](http://sparkandshine.net/local-install-wordpress-debugging-and-upload-to-remote-server/#4)
[^3]: [wamp - you don't have permission to access [custom alias] on this server - Stack Overflow](http://stackoverflow.com/questions/26170331/you-dont-have-permission-to-access-custom-alias-on-this-server)
[^4]: [Mysql root用户权限恢复解决办法 - cycao313的专栏 - 博客频道 - CSDN.NET](http://blog.csdn.net/cycao313/article/details/8454607)
