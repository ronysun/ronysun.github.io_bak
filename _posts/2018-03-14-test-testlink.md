---
layout: post
title: "testlink1.9.16的搭建"
categories:
  - test
---
# testlink1.9.16的搭建
在centos7.3上搭建testlink1.9.16，要求环境：
- apache(httpd)
- php version > 5.6
- mysql(mariadb) > 5.6：mysql-5.5版本在创建数据库时会报错  

centos7.3上php和mysql都需要升级
升级完成后，按照testlink的安装文档:
1. 进行logs和upload_area文件夹的创建和权限修改
2. 在浏览器上点击http://($ip)/testlink/install/index.php，进行安装操作
