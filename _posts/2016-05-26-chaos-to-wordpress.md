---
layout: post
title: "WordPress 开发小记"
date: 2016-05-26 11:40:00
categories: Programming
tags: 从零到一 WordPress
excerpt: "莫名躺枪, 部署了一个 WordPress 站点, 跳坑也填坑."
---

* content
{:toc}


前一阵莫名躺了个枪, 需要开发一个小站点. 为了后期不会写代码的用户们维护方便,
考虑要有所见即所得的后台界面为好. (Jekyll 虽然好, 静态站点部署也方便得多,
但在用户最大的前提下, 也完全没有列入考虑.)
很自然地选择了成熟的博客后台 WordPress. 于是跳了一些坑, 也填了一些坑, 是为记.

## 本地开发与调试

初期开发测试时, 最好能在本地部署, 以提高`开发-测试-反馈`循环的速度.
在 OS X 上, 可用打包好的 MAMP 来进行本地测试[^1].

### 安装 MAMP

MAMP stands for Macintosh, Apache, MySQL, and PHP.

这里需要注意的是使用 MAMP 即可(不需要 MAMP PRO)

### 设置 MAMP

WordPress 需要设置 MySQL 和 Apache 才能安装.

因此我们需要在 MAMP 界面和管理网站中进行设置. 按照官方教程设置即可.

### 安装 WordPress

设置好 MAMP 后, 我们就在本地构建了 wp 的安装环境. 下面按照[经典的 5 分钟安装](https://codex.wordpress.org/zh-cn:%E5%AE%89%E8%A3%85_WordPress)即可.

### 本地调试 WordPress

访问本机地址即可看到 WordPress 前台和后台.

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
Google 发现需要设置 MyPHPAdmin 访问数据库的权限, 使其它地址也能访问[^3].

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
然而我忘了把 WP 的用户先打开对应的权限. 于是, 所有的 MySQL 用户都不能访问表,
网站直接无法访问 :(

求助了万能的 Google, 发现可以通过 `mysqld -nt --skip-grant-tables`,
在启动 MySQL 时不检查用户权限. 再写 SQL 语句修改用户权限表(mysql.user), 即可恢复用户的权限[^4].

~~~sql
UPDATE USER SET select_priv='Y',Insert_priv='Y',Update_priv='Y',Delete_priv='Y',Create_priv='Y',Drop_priv='Y',Reload_priv='Y',File_priv='Y',Grant_priv='Y',References_priv='Y',Index_priv='Y',Alter_priv='Y',Show_db_priv='Y',Super_priv='Y',Create_tmp_table_priv='Y',Lock_tables_priv='Y',Execute_priv='Y',Repl_slave_priv='Y',Repl_client_priv='Y',Create_view_priv='Y',Show_view_priv='Y',Create_routine_priv='Y',Alter_routine_priv='Y',Create_user_priv='Y',Event_priv='Y',Trigger_priv='Y',Create_tablespace_priv='Y' WHERE user='root';
~~~

## IIS 反向代理之后 WordPress 重定向死循环及解决

站点部署之后, 为了公网访问, 我们采用 IIS 反向代理方法, 让一台有公网访问权限的服务器作为代理访问 WordPress 服务器. 即 `公网 - IIS 服务器 - WordPress 服务器`

然而反向代理架设之后产生了新的问题: 网站只有主页可以正常访问, 其余页面都出现了重定向死循环.

仔细测试发现:

- 后台 (*wordpress-domain/wp-admin*) 正常
- 直接访问 WordPress 主机的 ip 地址正常
- 预览页面 (*wordpress-page/?preview=true*) 正常

说明这一问题可能是正常访问页面时, WP 内部重定向到原地址的反向代理所导致.

尝试更改 `.htaccess` 多次, 未果.

重新配置 WordPress 站点名称和后台文件夹位置, 各种组合, 均未解决.

[StackOverflow 提问](http://stackoverflow.com/questions/38302343/wordpress-redirecting-after-iis-reverse-proxy), 几天后也没人回答. (最后自己去写了几句...)

最后又 Google 了一番, 在网站中找到了解决方案[^5], 直接修改 WordPress 文件夹中 `wp-includes/template-loader.php`, 注释掉下面的 `template_redirect ` 相关代码:

~~~php
//if ( defined('WP_USE_THEMES') && WP_USE_THEMES )
	/**
	 * Fires before determining which template to load.
	 *
	 * @since 1.5.0
	 */
//	do_action( 'template_redirect' );
~~~


## 修改 WordPress 主题字体, 并禁用 Google Fonts

对于中文字体在全平台的显示, 修改 `style.css` 中的 font-family[^6]:

~~~	
font-family: Helvetica, Tahoma, Arial, STXihei, "华文细黑", "Microsoft YaHei", "微软雅黑", SimSun, "宋体", Heiti, "黑体", sans-serif;
~~~

访问后台时, 由于 GFW, 加载 WordPress 自带的 Google Fonts 可能会导致加载时间很长. 可以使用插件禁用. [^7]

## 停止 WordPress 自动更新和更新提示

WordPress 的自动更新和更新提示对于架设在内网的服务器也很蛋疼, 一并停止[^8].

---

参考资料:

[^1]: [Installing WordPress Locally on Your Mac With MAMP « WordPress Codex](https://codex.wordpress.org/Installing_WordPress_Locally_on_Your_Mac_With_MAMP)
[^2]: [本地安装WordPress调试以及上传到远程服务器 Spark & Shine](http://sparkandshine.net/local-install-wordpress-debugging-and-upload-to-remote-server/#4)
[^3]: [wamp - you don't have permission to access [custom alias] on this server - Stack Overflow](http://stackoverflow.com/questions/26170331/you-dont-have-permission-to-access-custom-alias-on-this-server)
[^4]: [Mysql root用户权限恢复解决办法 - cycao313的专栏 - 博客频道 - CSDN.NET](http://blog.csdn.net/cycao313/article/details/8454607)
[^5]: [wordpress 重定向在squid反向代理下的问题解决方案](http://jacky.aiwaly.com/wp/wordpress-%E9%87%8D%E5%AE%9A%E5%90%91%E5%9C%A8squid%E5%8F%8D%E5%90%91%E4%BB%A3%E7%90%86%E4%B8%8B%E7%9A%84%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88.html)
[^6]: [wordpress主题字体更换 Anotherhome](https://www.anotherhome.net/1010)
[^7]: [Google Fonts导致WordPress 速度问题的三个解决方案 DeveWork](http://devework.com/google-fonts-in-wordpress.html)
[^8]: [WordPress 禁用自动更新和更新提示 WordPress大学](http://www.wpdaxue.com/disable-updates-manager.html)