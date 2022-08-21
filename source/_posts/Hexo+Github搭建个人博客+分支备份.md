---
title: Hexo文章撰写技巧
date: 2022-08-18 23:51:11
tags:
---



# Hexo+Github搭建个人博客+分支备份

本文只是简单做个记录，并非详细小白教程。



### 目录

- [Hexo的简单使用](https://blog.csdn.net/qq_45271256/article/details/105800705/#Hexo_9)
- [自定义文章URL](https://blog.csdn.net/qq_45271256/article/details/105800705/#URL_82)
- [开启分类以及标签等](https://blog.csdn.net/qq_45271256/article/details/105800705/#_102)
- [启用文章搜索功能](https://blog.csdn.net/qq_45271256/article/details/105800705/#_126)
- [启用sitemap](https://blog.csdn.net/qq_45271256/article/details/105800705/#sitemap_145)
- [开启RSS订阅](https://blog.csdn.net/qq_45271256/article/details/105800705/#RSS_151)
- [搜索引擎收录](https://blog.csdn.net/qq_45271256/article/details/105800705/#_167)
- [本地Hexo文件备份到Github分支](https://blog.csdn.net/qq_45271256/article/details/105800705/#HexoGithub_171)



# Hexo的简单使用

1、新建一个博客

```bash
$ hexo init <folder>
$ cd <folder>
$ npm install
123
```

新建完成后，指定文件夹的目录如下：

```bash
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
12345678
```

- _config.yml：网站的配置信息
- package.json：应用程序的信息
- scaffolds：模版文件夹，当新建文章时，Hexo 会根据 scaffold 来建立文件
- source：存放用户资源的文件夹
- themes：主题文件夹，根据主题来生成静态页面

2、常用命令

- init 新建一个博客

```bash
$ hexo init [folder]
1
```

- new 新建一篇文章

```bash
$ hexo new [layout] <title>
1
-p, --path	自定义新文章的路径
-r, --replace	如果存在同名文章，将其替换
-s, --slug	文章的 Slug，作为新文章的文件名和发布后的 URL
123
hexo new page --path about/me "About me"
1
```

以上命令会创建一个 source/about/me.md 文件，同时 title 为 “About me”

- generate 生成静态文件

```bash
$ hexo g
1
```

- server 启动服务器

```bash
$ hexo server
1
```

- deploy 部署网站

```bash
$ hexo d
1
```

- clean 清除缓存文件

```bash
$ hexo clean
1
```

# 自定义文章URL

编辑_config.yml文件：

```bash
permalink: :id.html
1
```

在写文章.md时，在文章首部加上id属性，id对应的值为该文章的URL

```bash
---
title: 【渗透测试】Google搜索语法的使用
id: google_hack
declare: true
tags:
 - 安全
 - 渗透测试
abbrlink: 10000
date: 1970-01-01 09:00:00
---
12345678910
```

如上，则该文章URL为`/google_hack.html`

# 开启分类以及标签等

以开启标签(Tags)功能为例：

```bash
hexo new page tags
1
```

生成以下文件：`source\tags\index.md`
修改`index.md`，添加 type 属性

```bash
---
title: Tags
date: 2020-01-21 08:00:00
type: "tags"
---
12345
```

写文章时在文章首部使用对应标签

```bash
tags:
 - 渗透测试
12
```

分类，关于等使用类似方法

# 启用文章搜索功能

```bash
$ npm install hexo-generator-searchdb --save
1
```

编辑`_config.yml`，新增以下内容

```bash
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
12345
```

编辑主题中的`_config.yml`文件，开启本地搜索功能

```bash
local_search:
  enable: true
12
```

# 启用sitemap

```bash
npm install hexo-generator-sitemap --save
1
```

# 开启RSS订阅

```bash
npm install hexo-generator-feed --save
1
```

全局_config.yml中添加以下信息：

```bash
feed:
  type: atom
  path: atom.xml
  limit: 20
1234
```

`hexo g` 生成`atom.xml`文件

# 搜索引擎收录

以`next`主题为例，主题_config.yml中自带了`site_verification`：

找到对应搜索引擎，验证时选择HTML标记验证，然后将对应的content的值填入`site_verification`中即可

# 本地Hexo文件备份到[Github](https://so.csdn.net/so/search?q=Github&spm=1001.2101.3001.7020)分支

- 1、建立hexo分支
  将自己的Github项目克隆到本地

```bash
git clone
1
```

- 2、删除除了`.git`以外的所有文件
- 3、将hexo blog相关文件全部复制到该文件夹
- 4、检查 `.gitignore` ， 包含以下需要忽略的内容
  ![在这里插入图片描述](../imgs/$%7Bfiilename%7D/20200428135909958.png)
- 5、 创建hexo分支并切换到该分支

```bash
git checkout -b hexo
1
```

- 6、上传到Github

```bash
git add --all
git commit -m ""
git push -u origin hexo
123
```

- 7、后续更新以及写文章

PS：如果主题是从Github克隆下来的，则会因为主题自身存在的`.git`文件夹导致无法成功上传主题文件。
解决办法：
清理缓存，删除主题中的`.git`文件

```bash
git rm -r --cached "文件夹的名称"
1
```

三步重新上传

**之后写文章可以在该项目下写，与之前一样，只不过这里同时管理了两个分支。**

```bash
master -负责展示静态网页
hexo -备份本地hexo文件
12
```

- master分支更新

```bash
hexo d
1
```

- hexo分支更新

```bash
git add . #添加所有文件到暂存区
git commit -m "新增博客文章"  #提交
git push origin hexo #推送hexo分支到Github
123
```

关于远程仓库的关联：

```bash
 git remote add origin git@github.com:XXX
1
```

- 8、新电脑环境下的恢复
  安装hexo以及相关依赖，克隆hexo分支到本地

```bash
git clone -b hexo git@github.com:XXX
1
```

写文章，hexo g, hexo d，发布到master分支
再更新备份到hexo分支，三步, `add`, `commit`, `pull`
