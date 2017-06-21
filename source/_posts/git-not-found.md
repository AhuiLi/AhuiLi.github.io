---
title: sudo :git: command not found
date: 2017-06-21 09:46:10
tags: [sudo,git,not found]
categories: linux
---

服务器：centos6.8

### 问题描述    
> 在利用git钩子实现自动化网站部署的时候，需要脚本执行```git pull origin feature```,因为直接执行会提示没有权限（我打印出当前执行用户，是root，但不知道为什么没权限），所以需要执行```sudo git pull origin feature```，这个时候总提示```sudo: git: command not found```

### 解决办法    
```/etc/sudoers```里面的这一行注释掉 ```#Defaults secure_path = /sbin:/bin:/usr/sbin:/usr/bin```

链接：[git 自动化部署](http://jiluz.com/2017/03/07/git-hooks/)
