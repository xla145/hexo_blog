---
title: Linux下项目自动部署脚本
date: 2019-02-18 17:06:43
---
这是本人第一次写shell 脚本，如果有一点错误的地方请谅解

因为平时上线部署项目都是手动进行的，大概分四步:

1：备份正在运行的项目文件

2：关闭Tomcat

3：复制需要部署的项目文件到Tomcat的weapps路径下

4：启动Tomcat，并打印启动日志信息

因为我们公司需要上线的项目版本，测试统一放在测试服务器的ftp下。线上项目部署在线上服务器，所以没有做从测试ftp拉取项目到线上服务器。这步操作需要自己手动将文件放置到线上服务器的某个路径，本人在线上服务器上创建了一个目录用于存放部署文件，并取名 online_delopy_project。

说了这么多了，接下里开始写第一个shell 脚本用于部署项目


    #!/bin/bash
    TOMCAT_HOME=/home/xxxx/apache-tomcat-7.0.84 #服务器路径
    BACKUP_HOME=/home/xxxx/yuelinghui-manage #备份文件的路径
    ONLINE_PROJECT_HOME=/home/xxxx/online_delopy_project#需要上线部署的文件路径
    PROJECT_VERSION=1.2.2 #项目的版本号
    cd $BACKUP_HOME
    new_file=yuelinghui_v${PROJECT_VERSION}_date +%Y%m%d #创建备份文件
    mkdir $new_file
    cp -rf $TOMCAT_HOME/webapps/ROOT/ $BACKUP_HOME/$new_file
    ${TOMCAT_HOME}/bin/catalina.sh stop || true
    sleep 1s #睡眠一秒 防止服务器没有关闭，就执行删除项目的操作
    echo ‘删除服务器中的项目，重新部署’
    rm -rf $TOMCAT_HOME/webapps/ROOT/
    echo ‘部署开始。。。。’
    cp -rf $ONLINE_PROJECT_HOME/* $TOMCAT_HOME/webapps/ROOT/
    echo ‘启动tomcat。。。。’
    ${TOMCAT_HOME}/bin/catalina.sh start || true
    sleep 1s
    echo ‘打印启用日志信息。。。。。。’
    tail -10f ${TOMCAT_HOME}/logs/catalina.out
    
    date +%Y%m%d 获取当前时间
