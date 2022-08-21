---
title: Hexo github
---
很多人都想搭建自己的博客，但是奈何技术有限，服务器部署，HTML，域名解析，等等，这一大堆东西可真有的折腾。
### 最近换了新电脑旧电脑的东西都没有了现在需要重新搭建一次博客

但是如果有这样一个机会，不需要太复杂的操作，不要服务器，不要域名，不用写html，你愿意去尝试吗？

可以先看一下我自己的https://github.com/tian857/tian857.github.io.git

当时也是费了半天功夫才搭建好，在这篇文章中会详细介绍整个流程，尽量避免入坑吧，但要是真是你自己给自己挖坑那就没办法了。

搭建的时候也是看着别人的教程一点点跟进，但是都有缺陷。
比如分支的创建，和私人域名的解析，有这方面的教程但是都不是太完善，有些只是教你搭建但是后期没有考虑换电脑，换环境的情况。包括后期维护等等。

这是我github上的源码，为了便于理解。本文相关部分也会结合源码进行讲解。有不清楚的地方欢迎提问，尽我能力给大家解决（毕竟我不是专业的）。
https://https://github.com/tian857/tian857.github.io.git

优点
1、不需要服务器，github免费提供的托管服务
2、不需要域名，github同样提供了一个.io的域名
3、不用自己写xml、hexo提供了很多开源的博客模板。
4、便于维护更新，换风格直接换模板就行。

### 进入正题###

安装相关工具
1、安装Node.js
https://nodejs.org/dist/v4.2.3/node-v4.2.3-x86.msi
https://nodejs.org/dist/v4.2.3/node-v4.2.3-x64.msi
选择需要的版本下载，分别是32位和64位。下载后安装一直默认下一步就行。
2、安装Git
Git-2.11.0.3-32-bit.exe
Git-2.11.0.3-64-bit.exe
打开cmd命令行(win+r 输入cmd回车)分别执行

node -v
npm -v
git --version



如果都可以成功运行出现版本信息证明安装成功。
这里写过一篇介绍git的文章，如果安装遇到问题可以去查看：
《必须要会的Git基本使用及常用命令操作》
3、安装Hexo
必须按照步骤来，因为hexo需要使用node.js的npm命令。
打开cmd命令行(win+r 输入cmd回车)分别执行

npm install hexo-cli -g
1
等待自动安装完成后。输入

hexo -v
1
检查是否安装成功
到这一步如果所有都安装成功，并测试无问题，那么本地配置已经搞定，下面开始在github上建项目了。

##### 在github上创建并设置远程库
注册登录略过，不会的请自行百度。
**登录后点击“+”号，选择New repository，下一步
在Repository name下面填写你要创建的地址，这个地址是就是你的域名，以github.io结尾。**例如填写wapchief.github.io以后就在地址栏直接输入wapchief.github.io就可以访问网站
按照提示一步步完成创建。
创建之后，点击setting设置远程库：
找到GitHub Pages点击Automatic page generator
等待github pages创建完成，然后输入你创建的地址测试github pager是否创建成功。此时页面是空的没有任何东西。

下面要做的就是使用hexo模板将源码放到github。

##### 初始化Hexo
创建一个文件夹用于存放hexo。然后右键该文件夹使用

git bash here
1
命令就可以直接定位到当前目录。

然后使用

 npm install hexo --save
1
初始化hexo基础配置文件。初始化时间比较长，不用急，等完成后输入

hexo init
1
初始化hexo。这个命令和git 仓库命令相似。意思一样都是初始化。
接着输入

npm install
1
配置node。然后输入

hexo g
1
加载hexo基础html、css、js等文件。
在这完成后等于已经在本地创建了一个网页，想查看的话，输入

hexo s
1
然后相当于开启了一个本地的服务器，会提示你拷贝url到浏览器。
(如图)
在浏览器输入 http://localhost:4000/
会看到这样的页面，证明本地hexo默认模板配置成功，

到这一步如果将hexo放到github page，那么使用github.io访问的将是一个默认的页面。下面我们要把他diy成自己的博客，包括导入模板主题等。

安装主题
关于主题大多都是以压缩包形式，可以去github上搜hexo theme，会出来很多，里面都有介绍。
比如我们要用第一个。
可以通过两种方法，这里我直接使用git命令完成，具体请参考next官方文档：http://theme-next.iissnan.com/getting-started.html
继续在我们的本地hexo目录输入

git clone https://github.com/iissnan/hexo-theme-next themes/next
1
等待下载完成。如果出现失败的提示，有可能是网络环境不好。重试即可。
ok，下载完主题后我们打开文件夹，找到themes文件夹，发现里面多了个
next
这个就是我们下载下来下来的主题文件。
接下来我们回到hexo根目录下用记事本打开_config.yml文件。
把landscape修改成next就行了。
我们再运行

hexo s
1
打开浏览器地址栏输http://localhost:4000/
现在是我们修改主题后的页面。

修改内容
内容较多建议参考官方文档
https://hexo.io/zh-cn/docs/configuration.html
修改完之后，可以重新执行hexo s在浏览器查看效果。并确认无误，包括以后需要添加文章，或者更新主题等，都建议先在本地查看无误再远程部署。
根_config.yml文件中

# Site
title: 网站标题
subtitle: 副标题
description: 个人签名
author: 姓名
language: zh-Hans
timezone:
这里有几个坑需要注意一下：

1、所有的配置“:”符号后面都要带空格，否则执行本地测试直接失败。
2、language是设置语言。zh-Hans是中文。
3、如果设置zh-Hans后仍出现乱码问题。需要更改文件的字符编码集为UTF-8,方法很多具体，就不详细介绍了。

注意同时要在，根_config.yml中配置自己的远程仓库地址

# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/tian857/tian857.github.io.git
  branch: master

这一步很关键，不要漏了。

提交项目，部署
继续在本目录命令行
安装部署工具（方便以后更新）
npm install hexo-deployer-git -save
1、初始化本地仓库：
git init
2、连接远程仓库：
如果是第一次使用git，在使用git的时候会提示输入用户名和密码，用户名是自己的注册邮箱。
git remote add origin https://github.com/wapchief/wapchief.github.io.git
3、发布hexo到github page
hexo clean && hexo g && hexo d
4、添加文件到本地仓库
git add
5、提交声明
git commit -m '内容'
6、推送到远程仓库（github）
这里建议创建一个新的分支hexo，用于管理hexo文件。提交的的时候只提交hexo网站html、css、等源文件。而默认的master用来部署更新项目,具体可以看我的github地址分支情况https://github.com/wapchief/wapchief.github.io
创建并切换到新建分支：
git checkout -b hexo
将分支推送到远程仓库：
git push origin hexo
这时打开网站就能看到效果了。

记得提交以后去github上把hexo分支设置默认，以后写文章等都要部署。
文章在hexo目录下的\source_posts文件夹中，是md格式，也就是Markdown格式。
以后可以用以下命令部署。也就是第三步

//等于一次性执行了，清空、刷新、部署三个命令
hexo clean && hexo g && hexo d
1
2
如果部署时不clean，可能之前修改的文章还存在。包括一些配置等，有时候部署完成后并没有改变，其实是缓存未清除。

进阶-绑定域名
域名可以自行去阿里云，景安等网站购买。一般都有系统提示步骤。
拿到域名之后，进行解析，推荐使用dnspod免费解析。
具体使用方法：
1、注册登录dnspod之后，添加域名，然后解析，添加两条记录


2、登录你注册域名网站的管理后台，修改DNS解析地址为

f1g1ns1.dnspod.net
f1g1ns2.dnspod.net
1
2
3、登录github，找到项目，设置setting，找到GitHub Pages


4、在master分支下的CNAME.MD文件写入注册的域名


再次访问差不多就好了，如果延迟的话，可能解析立马不会生效。需要等会儿才能看到

## 查看本机ssh公钥，生成公钥



### 查看ssh公钥方法：
  1.通过命令窗口


a.打开你的git bash 窗口
 b.进入.ssh目录： cd ~/.ssh
 c.找到id_rsa.pub文件： ls
 
d.查看公钥： cat id_rsa.pub    或者 vim id_rsa.pub
如图：
2、或者直接一步输入命令： cat ~/.ssh/id_rsa.pub
3、或者直接在C:\Users\user\.ssh文件夹下查找
说明： 在linux系统和window系统下都可以使用命令行查看 （注意：window系统，需要在git bash下使用）