---
title: gitLab 搭建
date: 2019-02-22 10:42:28
tags:
- github
- linux
- CentOS
---
今天我们说一下怎么在Linux搭建gitLab，具体步骤如下：

打开该地址 [https://about.gitlab.com/installation/](https://about.gitlab.com/installation/#centos-7) 这个是官方提供安装文档，具体我们来操作一下

1：根据操作系统版本寻找对应的文档

2：安装和配置相应的依赖

On CentOS 7 (and RedHat/Oracle/Scientific Linux 7), 以下命令可以使http和ssh允许在防火墙下能被访问
>
> ```
> sudo yum install -y curl policycoreutils-python openssh-server
> sudo systemctl enable sshd
> sudo systemctl start sshd
> sudo firewall-cmd --permanent --add-service=http
> sudo systemctl reload firewalld
> ```

接下来，安装 Postfix 用于发送邮件。如果你想用其他的方式发送邮件，请跳过该步骤并在gitLab 安装之后，配置其他的邮件服务器。
>
> ```
> sudo yum install postfix
> sudo systemctl enable postfix
> sudo systemctl start postfix
> ```

3：添加GitLab软件包存储库并安装软件包

添加GitLab软件包存储库
>
> ```
> curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash
> ```

接下来，安装gitLab。安装过程是自动的，安装成功后通过URL 访问，如果你需要配置https，请参考 https://docs.gitlab.com/omnibus/settings/nginx.html#enable-https
>
> ```
> sudo EXTERNAL_URL="http://gitlab.example.com" yum install -y gitlab-ee
> ```

```
EXTERNAL_URL 填写你要访问的地址，因为现在在测试环境我们用ip代替（192.168.1.6）
```

完成以上步骤，gitLab算是搭建完成了。

GitLab常用命令
>
> ```none
> sudo gitlab-ctl start    # 启动所有 gitlab 组件；
> sudo gitlab-ctl stop        # 停止所有 gitlab 组件；
> sudo gitlab-ctl restart        # 重启所有 gitlab 组件；
> sudo gitlab-ctl status        # 查看服务状态；
> sudo gitlab-ctl reconfigure        # 启动服务；
> sudo vim /etc/gitlab/gitlab.rb        # 修改默认的配置文件；
> gitlab-rake gitlab:check SANITIZE=true --trace    # 检查gitlab；
> sudo gitlab-ctl tail        # 查看日志；
> ```

gitlab如何使用，登录gitLab后,慢慢玩吧！玩多了就会了

gitLab一些小问题

1:复制项目的url地址，发现port丢失

解决：打开 /var/opt/gitlab/gitlab-rails/etc 目录

修改 gitlab.yml 文件，将端口改成你想要的端口号 ! !
![gitLab-搭建](gitLab-搭建/1550803434.jpg)