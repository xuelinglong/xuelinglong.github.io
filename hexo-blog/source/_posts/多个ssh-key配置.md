---
title: '多个ssh-key配置'
date: 2018-04-26 20:45:39
tags: 搭建工作环境
---

# 多个ssh-key配置

## 配置单个ssh

使用个人github时配置过ssh，当时在一台电脑上面只配置了一个key，因此没有出现问题。

配置过程：

```配置过程
$ ssh-keygen -t rsa -C "youremail@email.com"
// 之后会让输入新生成的key的名字，输入两次设置多密码。
// 若不输入名字则默认生成id_rsa，id_rsa.pub
$ cat id_rsa.pub
// 拷贝公钥内容并添加在GitHub中。
$ ssh -T git@github.com
// 测试
```

## 多个ssh

入职第一天使用公司账号，因此电脑中需要两个ssh-key，一个是我个人github账号的，一个是公司gitlab账号的，按照上面单个ssh生成过程配置就出现问题了，测试时一直让输入密码，但是生成key时没有设置密码，因此不明白让输入的是什么密码，之后询问同事和Google搜索一步步解决了问题。解决过程如下：

最初不明白ssh的运作过程，因此生成一对名为office_gitlab，office_gitlab.pub的无密码密钥对，把公钥添加在公司账号的gitlab上面，测试时一直让输入密码，输入了许多印象中可能的密码都测试失败，因此萌生了是不是非得使用id_rsa这个名字才行，就把之前个人GitHub密钥对的名字改了，把office_gitlab改成id_rsa，把office_gitlab.pub改成id_rsa.pub，之后测试公司账号可以测试成功，但是个人GitHub账号测试就出问题了：

```问题过程再现
$ ssh-keygen -t rsa -C "youremail@email.com"
// 输入新生成的key的名字office_gitlab，不输密码
// 若不输入名字则默认生成id_rsa，id_rsa.pub，会覆盖之前的id_rsa密钥对！！！
$ cat id_rsa.pub
// 拷贝公钥内容并添加在GitHub中。
$ ssh -T git@git.xxx.net
// 测试时让输入密码，记得之前没有设置密码，因此发现出错了。
$ mv id_rsa id_rsa_bak
$ mv id_rsa.pub id_rsa_bak.pub
$ mv office_gitlab id_rsa
$ mv office_gitlab.pub id_rsa.pub
$ ssh -T git@git.xxx.net
// 测试成功
$ ssh -T git@github.com
// 个人GitHub测试失败，因此这个办法不能解决多个ssh的问题
```

之后查阅别人的博客发现多ssh有两种解决方案，第一种（ssh-add）：

```ssh-add解决方案
// 默认会使用id_rsa，因此多个key时需要使用ssh-add命令把其余的私钥添加到id_rsa中
$ cd ~/.ssh
$ ls
id_rsa id_rsa.pub id_rsa_bak id_rsa_bak.pub
$ ssh-add id_rsa_bak
$ ssh -T git@github.com
// 测试成功
// 但是此方法有局限性，每次重启电脑之后都要执行ssh-add才行
```

第二种（配置config）：

```配置config解决
// 在~/.ssh文件夹中新建config文件配置如下：
# gitlab
Host git.xxx.net
HostName git.xxx.net
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa //github对应的私钥
# coding.net
Host github.com
User youremail.com  // github对应的email
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa_bak //coding对应的私钥
$ ssh -T git@github.com
// 测试成功，重启也是如此
$ ssh -T git@git.xxx.net
// 测试成功，重启也是如此
```
