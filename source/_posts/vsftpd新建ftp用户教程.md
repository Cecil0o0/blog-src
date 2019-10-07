---
title: vsftpd新建ftp用户教程
tags:
  - aliyun
  - vsftpd
url: 17.html
id: 17
categories:
  - note
date: 2017-07-17 13:39:15
---

新建用户
----

1. useradd -d <path/to/folder> <username>
2. passwd <username>
3. usermod -s /usr/sbin/nologin <username>

修改配置文件
------

* vim /etc/vsftpd.conf

修改或添加如下行： listen=YES anonymous\_enable=NO local\_enable=YES write\_enable=YES chroot\_local\_user=NO chroot\_list\_file=/etc/vsftpd.chroot\_list

* vim /etc/vsftpd.chroot_list

在单独一行添加<username>

重启vsftp
-------

* service vsftpd restart

登陆FTP
-----

* ftp://<username>@hostname
