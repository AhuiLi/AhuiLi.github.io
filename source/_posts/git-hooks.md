---
title: git hooks自动化部署及踩坑全过程
date: 2017-03-07 09:46:48
tags: [git,钩子,githooks]
categories: linux
---

由于git每次有提交，服务器端都需要手动pull，很麻烦，于是研究一下git hooks想实现自动化网站部署。

网上搜索:随便一搜都能出来下面一段这样的代码，然后告诉你这就完成了。本以为copy过来真的就好使了，但事实总会不那么尽如人意。
<!--more-->
```shell
#!/bin/sh
unset GIT_DIR
NowPath='pwd'
DeployPath=/home/user/testDeploy #代码的仓库目录
cd $DeployPath
git pull origin master
cd $NowPath
exit 0
```
搜了半天代码基本上就是这个版本

下面说一下反复失败之后实现自动化部署的个人理解：   

**1. 远程裸仓库和远程服务器端代码仓库** 

需要理解清楚的是，我们实现git自动化项目部署的流程应该是__每次提交到远程裸仓库，裸仓库接收到push之后自动给代码仓库执行pull的操作。__
这里裸仓库应该与服务器代码库在一台服务器上，对裸仓库和代码库不理解的自行google。了解原理之后，就要奔着这个目标去实现。

**2. git hooks出来管事**

重点是上面提到裸仓库接收push之后怎么自动去执行pull操作，git仓库中有个.git文件夹，里面有个hooks文件夹，git 钩子就藏在这。里面有很多文件，其中有个`post-receive.sample`文件（没有的话也没有关系，新建一个`post-receive`文件，注意不带sample后缀，带上这个后缀默认是不触发执行的），我们要做的脚本代码就在这里个文件里面完成。上面提到的代码就是放到这个文件里面的。上面代码copy进去试一下，发现根本没有成功。

**3. 权限在作怪**

* 给git用户添加root公匙免密

脚本其实确实执行了，但是正常`git pull`需要输入git用户的密码，脚本里面没法输入密码，所以就需要让你的root用户免密码。这时就需要把root用户的公匙添加到`/home/git/.ssh/authorized_keys`文件中。
>>生成公匙，比如root用户：执行命令`ssh-keygen -t rsa -C"你的邮箱或者别的"`然后提示你输入密码，不用输一直回车就ok了，然后去`~/.ssh/`下面就可以看到`id_rsa`和`id_rsa.pub`文件了，把pub里面的内容放到上面的git用户的文件中就可以了。

但是，做完这些会发现测试提交还是有问题。   

* 给git用户添加sudo权限

找了半天发现还有一个权限的问题，就是当触发`post-receive`钩子时，用户没有权限在网站目录执行`git pull origin master`，所以会一直操作不成功。我们需要给git用户sudo的权限，`vim /etc/sudoers`找到`root    ALL=(ALL)       ALL`这一行，在下面加上`git   ALL=<ALL>   NOPASSWD:ALL`
`NOPASSWD`是sudo时免密。

**4. 最终实现**

前面的准备工作已经完成，现在只需要稍微修改上面的代码就可以了。
```shell
#!/bin/sh
unset GIT_DIR
NowPath=`pwd`
DeployPath="/mnt/arthur" #代码的仓库目录
echo $DeployPath
cd $DeployPath # 切换到代码目录
echo "****************************"
sudo git pull origin master # 执行pull操作
echo "******************"
sudo chown -R www:www $DeployPath # 把网站文件所有者改为原来的，因为sudo执行pull后会把改动的文件的所有者变为root
cd $NowPath
exit 0
```
对了，编辑完`post-receive`文件，要赋予其执行权限，`chmod +x post-receive`，并且改变文件所有者`chown git:git post-receive`。
这时候在本地电脑提交一下，看到如下面所示，即表示成功啦！
```
$ git push origin master
Counting objects: 7, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (4/4), 1.27 KiB | 0 bytes/s, done.
Total 4 (delta 1), reused 0 (delta 0)
remote: /mnt/arthur
remote: **************************** # 这是脚本打印的，两行星号中间的是自动执行pull返回的成功信息
remote: From server:/home/repo/arthur
remote:  * branch            master     -> FETCH_HEAD
remote: Updating 4473606..67b1561
remote: Fast-forward
remote:  hexo/git-hooks.md | 36 +++++++++++++++++++++++++++++++-----
remote:  1 file changed, 31 insertions(+), 5 deletions(-)
remote: ******************
To git@server:/home/repo/arthur.git
   4473606..67b1561  master -> master
```

**总结**

先弄清楚自动化部署原理，然后往这个原理方向去具体实施。上面每一步都要成功，不要急于查看结果，比如给git用户添加公匙，要现在网站仓库用root用户pull一下试试是否需要密码，不需要则这一步才算成功，然后进行下一步。有问题欢迎交流，指正。