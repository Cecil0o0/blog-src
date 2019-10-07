---
title: nginx配置https
tags:
  - DV SSL
  - https
  - nginx
  - pem
url: 110.html
id: 110
categories:
  - technology
date: 2017-08-06 11:24:52
---

- HTTPS 所需文件

1. .crt(Cerificate)或.pem
2. .key

- 著名 CA 机构

> StartCom(Class1 for free)，Symantic

- 申请 Class 1 DV SSL Certificate 过程

1. 登录 StartCom,进入 Certificates Wizard
2. 选择 Free User -> DV SSL Certificate
3. 验证域名
4. 用 openssl 生成并提交 CSR（Cerificate Signing Request）
5. 成功之后 download ZIP，压缩包中有多种 Server 的 crt
6. 将 nginxServer 里的 crt 文件上传至阿里云并配置 nginx.conf 文件

    server {
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;

            rewrite ^(.*) https://$server_name$1 permanent;

            error_log /var/log/wp-error.log;
            # Make site accessible from http://localhost/
            server_name blog.qingf.me;

    }
    server {
    listen 443;
    server_name blog.qingf.me;

            location / {
              proxy_pass https://cecil0o0.github.io/;
            }

            error_log /var/log/wp-error.log;

            ssl on;
            ssl_certificate /root/214219488470845.pem;
            ssl_certificate_key /root/214219488470845.key;

            ssl_session_timeout 5m;

            ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2;
            ssl_ciphers "HIGH:!aNULL:!MD5 or HIGH:!aNULL:!MD5:!3DES";
            ssl_prefer_server_ciphers on;

    }

- 结果

1. Chrome NET::ERR_CERT_REVOKED
2. Safari 访问用户是否接受
3. quark 等手机浏览器可直接访问

- NET::ERR_CERT_REVOKED 问题尝试解决

1. 在[官网](https://www.startcomca.com/root)中了解到证书链结构
2. 下载 CAG3(StartSSL 根证书)和 BR SSL ICA(中级 CA 证书)
3. 合并证书生成证书链

    cat chenhaojie.crt StartComCertificationAuthorityG3.cacert.pem StartComBRSSLICA.cacert.pem > nginx_server.crt

> 注:**必须**对 nginx_server.crt 处理，可能存在证书头尾相连，需手动换行。另外可能需要 dos2unix 删除^M。

1. 最后发现是 Chrome 和 FireFox 不信任 StartCom Certification Authority G3 的证书。GG。

### 最终用了阿里云的 DV SSL 证书（Symantec）=。=
