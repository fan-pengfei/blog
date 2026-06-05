---
title: "Hexo+Yilia搭建自己的博客"
date: 2021-08-17T07:19:40.000Z
draft: false
tags: ["DIY", "兴趣"]
---
> 搭建自己的博客；

# 缘起

> 一直想着要搭建一个属于自己的博客，感觉这样子很酷很酷，也是正好记录一下自己学习中遇到的问题，就当做一个笔记本来用吧！

原先想着用树莓派做服务器就可以搭建自己的的个人网站用来写博客，先在阿里云上购买了域名，其实最后是买了两个：

```bash
fan-pengfei.top
fan-pengfei.xyz
```

为啥买了两个呢？

# 波折

> 其实是我自己太粗心大意了，在二月份的时候自己就买了.xyz这一个域名，后来忙其他的事就把这件事搁置了；注册新域名的时候发现这个已经被注册了（没想到是自己之前注册的），所以没办法，只能感慨与自己同名同姓的人真多，然后就注册了.top域名;

注册完才发现，自己的域名控制台上竟然有两个域名，这才让我想起尘封已久的记忆，不过头一个快过期了，就用第二个搭建了这个个人网站；

> [https://fan-pengfei.top](https://fan-pengfei.top)

前两天闲来无事，就又想折腾一下搭建自己博客的事；找了很多资料，终于还是将这个博客搭建起来了，挺简约的，自己很喜欢，毕竟博客就是用来记录自己学习到的知识，所以博客的内容应该更加重要。

# 步骤

## 一、配置Github

首先注册、登录： [https://github.com/](https://github.com/)

记住自己的Username（很重要）；

然后右上角选择 Create a new repository；

`Repository name` ->填自己的名字, `yourname.github.io`->这个就是你博客的域名了(yourname与你的注册用户名一致)；

例如，我的域名是`github.com/fan-pengfei`，就填入`fan-pengfei.github.io`；

## 二、配置环境

安装 Node.js： [https://nodejs.org/en/](https://nodejs.org/en/)

安装 Git： [https://github.com/waylau/git-for-win](https://github.com/waylau/git-for-win)

安装完成后，在开始菜单里找到Git->Git Bash，打开，并依次执行以下命令：

```bash
git config --global user.name "username"
git config --global user.email "useremail"
```

其中名称和邮箱都是Github注册时自己的名字和邮箱；

安装 Hexo，所有必备的应用程序安装完成后，即可使用 npm 安装 Hexo:

```bash
npm install -g hexo-cli
```

至此环境安装完毕（推荐使用cmder，超级好用的）;

## 三、电脑设置

在电脑E盘（自己随意）目录下新建文件夹my_blog，进入my_blog，按住Shift键点击鼠标右键，选择`Cmder Here`；因为我有安装Cmder，没有安装的点击“在此处打开命令窗口”，输入：

```bash
hexo init blog
```

稍微等待下，速度有点慢，成功后将提示：

```bash
INFO  Start blogging with Hexo!
```

重新打开CMD，输入：

```bash
ssh-keygen -t rsa -C "Github的注册邮箱地址"
```

一路Enter过来就好，得到信息：

```bash
Your public key has been saved in /c/Users/user/.ssh/id_rsa.pub.
```

找到该文件，打开（sublime text），Ctrl + a复制里面的所有内容，然后进入Sign in to GitHub：[https://github.com/settings/ssh](https://github.com/settings/ssh)

New SSH key ->Title，blog ->Key：输入刚才复制的—— Add SSH key；

## 四、配置博客

在blog目录下，用Notepad++或者电脑自带的记事本打开_config.yml文件（我的路径是E:\my_blog\blog），修改参数信息；

特别注意的是，在每个参数之后的：之后都要加上一个空格，否则一定会报错；

修改网站相关信息：

```bash
title: 小飞的博客
subtitle: 副标题
description: 网页描述
author: 小飞
language: zh-CN
timezone: Asia/Changsha
```

配置部署（我的是fan-pengfei，修改成自己的）：

```bash
deploy:
  type: git
  repo: https://github.com/fan-pengfei/fan-pengfei.github.io.git
  branch: main
```

## 五、发表文章

在命令行窗口中输入：

```bash
hexo new "测试文章"
INFO  Created: E:\my_blog\blog\source\_posts\测试文章.md
```

找到该文章，打开，使用Markdown语法编辑即可；

保存，然后执行下列步骤：

```bash
E:\my_blog\blog
$ hexo clean
INFO  Deleted database.
INFO  Deleted public folder.

E:\my_blog\blog
$ hexo generate
INFO  Start processing
INFO  Files loaded in 1.48 s
#省略
INFO  29 files generated in 4.27 s
```

最后一步，发布到网上，执行：

```bash
F:\test\blog
$ hexo deploy
INFO  Deploying: git
INFO  Clearing .deploy_git folder...
INFO  Copying files from public folder...
#省略
```

其中会跳出Github登录，直接登录即可；

## 六、总结

发布文章的步骤：

1、hexo new 创建文章；

2、Markdown语法编辑文章；

3、部署（所有打开CMD都是在blog目录下）；

```bash
hexo clean #清除缓存 网页正常情况下可以忽略此条命令
hexo generate #生成
hexo server #启动服务预览，非必要，可本地浏览网页
hexo deploy #部署发布
```

常用命令简写Tips：

> hexo n “我的博客” == hexo new “我的博客” #新建文章hexo g == hexo generate#生成hexo s == hexo server #启动服务预览hexo d == hexo deploy#部署

如果在执行 hexo deploy 后,出现`error deployer not found:github`的错误，执行：

```bash
npm install hexo-deployer-git --save
```

## 七、其他

> 若想让让博客更美观，可以更换其他主题，我使用的Yilia主题具体安装步骤请参考：[https://github.com/litten/hexo-theme-yilia、](https://github.com/litten/hexo-theme-yilia、)

删除增加图片：

> 是在E:\my_blog\blog\themes\yilia\source\assets文件夹下

我的博客截图：

![QQ图片20210817202532](img-1.png)
