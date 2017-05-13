---
title: crontab定时任务
date: 2017-05-13 09:40:48
tags: [crontab,定时任务]
categories: linux
---


最近需要做一些服务器的自动备份，就用到了linux的定时命令crontab。   

# 查看和编辑crontab文件

- 查看当前用户是否有计划任务，`crontab -l`  查看。
- 如果有任务直接 `crontab -e`进行编辑。
- crontab格式说明
  每个任务都由六个栏位组成，每个含义如下

<!--more-->

| 意义   | 分钟   | 小时   | 日期   | 月份   | 星期   | 命令      |
| ---- | ---- | ---- | ---- | ---- | ---- | ------- |
| 范围   | 0-59 | 0-23 | 1-31 | 1-12 | 0-7  | command |

**注意：** 0或者7都代表周日（星期天）
例如：`10 2 1 * *  /home/ahui/bakup.sh`*号表示任意，这个任务表示每月1号的凌晨2点10分执行home下的那个备份脚本。

# 新建crontab文件

- 如果第一步查看不存在计划任务，这时候需要我们新建crontab文件。
- 新建mycrontab.cron文件，内容按照上面编辑方式写入你需要的任务，如：`10 2 1 * *  /home/ahui/bakup.sh`
- 然后执行`crontab mycrontab.cron`之后，用`crontab -l`就可以查看到刚才添加的任务了。如果想再编辑，重复上面的查看和编辑步骤。

**注：** 如果想更深入了解`crontab`，访问[文章1](http://www.cnblogs.com/peida/archive/2013/01/08/2850483.html)，[文章2](http://www.cnblogs.com/ggjucheng/archive/2012/08/19/2646763.html)。