
很早就想搭建一个属于自己的博客，也试过博客园、CSDN的博客，但是总感觉不是真正属于自己的博客，而且看到广告很烦。偶然发现一篇Hexo+github搭建博客的教程，就开始了尝试。
本篇为从零搭建博客的教程，其他功能等待后续文章。
<!--more-->

------------
## 一、环境依赖
##### 首先你需要一台可以连接外网的linux主机（本文以[阿里云ESC服务器](https://www.aliyun.com/product/ecs?spm=5176.8142029.388261.249.5ce776f46y6cDt "阿里云ESC服务器")为例，学生认证后购买有优惠），服务器相关配置相关内容不再赘述

##### 服务器环境及版本
```shell
node····8.9.3
git·····1.8.3.1
hexo····3.6.0
java····1.8.0_161 # 使用其他服务器中间件时不需要
tomcat··9.0.5 # 使用其他服务器中间件时不需要
hexo-admin # hexo博客管理工具
```

------------

## 二、环境配置
本文所有安装路径在`/opt`目录结构下，所有目录结构仅供参考
####  安装java，tomcat
##### 也可用其他服务器中间件，在此不做说明

####  安装node.js
- ##### 下载
 找到[最新的下载地址](http://nodejs.cn/ "最新的下载地址")，用wget命令下载到本地
```shell
cd /opt
mkdir node
cd node
wget http://cdn.npm.taobao.org/dist/node/v8.9.3/node-v8.9.3-linux-x86.tar.gz
```
- ##### 解压文件
```shell
tar -zxvf node-v8.9.3-linux-x86.tar.gz
```
- ##### 配置环境变量
```shell
# 编辑 /etc/profile (使用vim)
vim /etc/profile
# 按A插入编辑 vim编辑不在此赘述
# 在底部添加 PATH 变量
export NODE_HOME=/opt/node/node-v8.9.3-linux-x64
export PATH=$PATH:$NODE_HOME/bin
# 保存退出，先按esc键，再输入
:wq!
# 最后保存并使其生效即可
source /etc/profile
```
- ##### 检查环境
```shell
# 检查node.js版本
node -v
```
输出如下内容即表示安装成功
```shell
v8.9.3 # 安装的版本号
```

####  安装git
- ##### 安装流程
```shell
yum install git
```
- ##### 检查环境
```shell
# 检查git.js版本
git --version
```
	输出如下内容即表示安装成功
	```shell
	git version 1.8.3.1 # 安装的版本号
	```
	
####  配置github page
- ##### Github的优点
GitHub是基于git实现的代码托管。git可能是目前最好用的版本控制系统了，非常受欢迎。
GitHub可以免费使用，并且快速稳定。
Github上面的世界很精彩，用久了你的眼界会开阔很多。

- ##### 什么是Github Pages?
Github Pages可以被认为是用户编写的、托管在github上的静态网页。[GitHub Pages](https://pages.github.com/ "GitHub Pages") 本用于介绍托管在GitHub的项目，不过，由于他的空间免费稳定，用来做搭建一个博客再好不过了。
[![github page首页](http://wagk.oss-cn-huhehaote.aliyuncs.com/hexo/image/github-page-index.jpg "github page首页")](https://pages.github.com/ "github page首页")
- ##### 为什么要使用Github Pages
可以绑定你的域名(但暂时貌似只能绑定一个)。 
简单快捷，使用Github Pages可以为你提供一个免费的服务器，免去了自己搭建服务器和写数据库的麻烦.
- ##### 注册GitHub账号
在创建博客之前，当然必须有GitHub的帐号，该帐号将用来创建项目，默认的域名username.github.com/projectName中的username也要用到这个帐号。
>注意：下面涉及到的一些命令凡是更用户名和项目名有关的一律会用这里的username和projectName代替，注意替换 
>访问：[http://www.github.com/](http://www.github.com/ "http://www.github.com/")
>注册你的username和邮箱，邮箱十分重要，GitHub上很多通知都是通过邮箱的。比如你的主页上传并构建成功会通过邮箱通知，更重要的是，如果构建失败的话也会在邮件中说明原因。

- ##### 创建项目仓库
在创建博客之前，还需要用已有的帐号创建一个项目，上面那个链接的projectName将是这里即将创建的项目名称。在Git中，项目被称为仓库(Repository)，仓库顾名思义，当然可以包含代码或者非代码。将来我们的网页或者模板实际上都是保存在这个仓库中的。
 1. 登录后，点击头像旁边+号选择`new repository`创建仓库，如下图
![新建github仓库入口](http://wagk.oss-cn-huhehaote.aliyuncs.com/hexo/image/new%20repository.jpg "新建github仓库入口")
 2. 填写仓库信息，如下图
 ![填写github page信息](http://wagk.oss-cn-huhehaote.aliyuncs.com/hexo/image/new%20repository%20info.jpg "填写github page信息")
 > 1. 每个帐号只能有一个仓库来存放个人主页，而且仓库的名字必须是`username/username.github.io`这是特殊的命名约定。你可以通过https://username.github.io 来访问你的个人主页。
> 2. 上图`Repository name`必须为`username.github.io`其中`username`为你的用户名，和前面`Owner`相同；`Description` 为仓库描述，根据需要填写
> 3. 创建了仓库后，我们就需要管理它，无论是管理本地仓库还是远程仓库都需要Git客户端；Git客户端实际上十分强大，它本身就可以offline的创建本地仓库，而本地仓库和远程仓库之间的同步也是通过Git客户端完成的。
- ##### SSH配置
上传文件需要配置ssh key，不然无法上传。
 1. 首先先检查一下本地是否已经存在ssh key,在Git Bash输入以下指令（任意位置点击鼠标右键），检查是否已经存在了SSH keys。
```shell
ls -al ~/.ssh
```
 2. 如果不存在就没有关系，如果存在的话，在保证没有其他人使用的情况下可以直接删除`.ssh`文件夹里面所有文件：
 如果存在将显示为：
```shell
total 20
drwx------  2 root root 4096 Mar  1 17:05 .
dr-xr-x---. 8 root root 4096 Mar  2 10:28 ..
-rw-------  1 root root    0 Mar  1 14:11 authorized_keys
-rw-------  1 root root 1679 Mar  1 16:55 id_rsa
-rw-r--r--  1 root root  397 Mar  1 16:55 id_rsa.pub
-rw-r--r--  1 root root 1197 Mar  1 17:12 known_hosts
```
 3. 设置name和emai
 ```shell
 git config --global user.email "you@example.com"
 git config --global user.name "Your Name"
```
需要注意的是这里的name是随意的，邮箱是你的联系邮箱，与github上的邮箱没有什么联系（不过我都是同一个邮箱）。
 4. 生成ssh 密钥
 ```shell
ssh-keygen -t rsa -C "username@domain.com" # 邮箱就是你注册Github时候的邮箱
```
按三次回车键即可，如果设置了密码请记住。
这一步在~/.ssh/下生成了两个文件id_rsa 和 id_rsa.pub
 5. 获取Key
 ```shell
cat ~/.ssh/id_rsa.pub
```
然后复制key
 6. 在Github上添加SSH密钥
   1. 点击头像选择下方Setting进入[设置界面](https://github.com/settings/profile "设置界面")，如下图
   [![设置界面入口](http://wagk.oss-cn-huhehaote.aliyuncs.com/hexo/image/setting.jpg "设置界面入口")](https://github.com/settings/profile "设置界面入口")
   在左侧选择`SSH and GPG keys`，如下图
   [![SSH key 入口](http://wagk.oss-cn-huhehaote.aliyuncs.com/hexo/image/SSH.jpg "SSH key 入口")](https://github.com/settings/keys "SSH key 入口")
   我这里已经填写了两组key，选择上方`New SSH key`新建 SSH key
   [![SSH key 管理](http://wagk.oss-cn-huhehaote.aliyuncs.com/hexo/image/key.jpg "SSH key 管理")](https://github.com/settings/keys "SSH key 管理")
   粘贴刚刚复制的key到下方输入框，`Title`为key的自定义名称
   [![新建SSH key](http://wagk.oss-cn-huhehaote.aliyuncs.com/hexo/image/new%20key.jpg "新建SSH key")](https://github.com/settings/ssh/new "新建SSH key")
   之后保存完成


####  安装hexo
- ##### 安装
```shell
cd /opt
# 创建目录
mkdir blog
# 安装 Hexo
npm install -g hexo
# 初始化 Hexo
hexo init blog
```
- ##### 安装插件
```shell
# 安装插件
npm install
# 如果使用过程中确实某些插件的，可根据以下插件检查
npm install hexo-generator-index --save
npm install hexo-generator-archive --save
npm install hexo-generator-category --save
npm install hexo-generator-tag --save
npm install hexo-server --save
npm install hexo-deployer-git --save
npm install hexo-deployer-heroku --save
npm install hexo-deployer-rsync --save
npm install hexo-deployer-openshift --save
npm install hexo-renderer-marked --save
npm install hexo-renderer-stylus --save
npm install hexo-generator-feed --save
npm install hexo-generator-sitemap --save
```
- ##### 修改Hexo配置文件
> 配置文件路径： `./_config.yml` 以下为我的配置，仅供参考
> 提示：key对应没有值的时候，冒号后面一定要有空格！否则会报错
> 如: timezone:会报错，timezone: 则不会。

- ##### Hexo配置文件参考
```shell
# Hexo Configuration Hexo配置文件
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/
# 网站信息
#标题
title: 某某の博客
#副标题
subtitle: 博客的副标题
#网站描述
description: 博客的描述
#作者昵称
author: 作者
#网站语言，默认英语，设置简体汉语
language: zh-CN
#时区，默认电脑时区
timezone: Asia/Shanghai
# 网址设置
#如果网站是放在子目录中，将url设置成'http://yoursite.com/child'，将root设置成'/child/'
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
#网址
url: http://www.domain.com
#网站根目录。如果网站是放在子目录中，将root设置成'子目录名'
root: /
#文章链接地址格式 。即文章存放的目录。
permalink: :year/:month/:day/:title/
permalink_defaults:
# 目录设置
#资源文件夹，放在里面的文件会上传到github中
source_dir: source
#公共文件夹，存放生成的静态文件
public_dir: public
#标签文件夹，默认是tags。实际存放在source/tags中。
tag_dir: tags
rss_dir: rss
#档案文件夹，默认是archives。
archive_dir: archives
#分类文件夹，默认是categories。实际存放在source/categories中。
category_dir: categories
#代码文件夹，默认是downloads/code
code_dir: downloads/code
#国际化文件夹，默认跟language相同
i18n_dir: :lang
#不需要渲染的文件夹或文件夹,放在[]中
# 这两个文件是百度和google的站长验证文件，不能渲染，否则会改变内容，不能验证过
skip_render: [baidu_verify_R9MZjdMkXT.html, google0f8fac7da2b48ef8.html, README.md, 模板.md]
# 写作选项
# 新建博文（帖子）的默认名称
# File name of new posts
new_post_name: :title.md
#默认布局模板是post，而不是draft和page
default_layout: post
#是否将标题转换成标题形式（首字母大写）
titlecase: false # Transform title into titlecase
#在新标签页面中打开网页
external_link: true # Open external links in new tab
filename_case: 0
#是否渲染草稿
render_drafts: false
#启动 Asset 文件夹
post_asset_folder: false
#把链接改为与根目录的相对位址
relative_link: false
#显示未来的文章
future: true
#代码块的设置
highlight:
  enable: true          #  使用代码高亮
  line_number: true # 显示行号
  auto_detect: true  # 自动检测语言
  tab_replace:
# 分类和标签
# 默认分类
default_category: uncategorized
#分类别名
category_map:
#标签别名
tag_map:
# 日期和时间格式
#Hexo 使用 Moment.js 来解析和显示时间。
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss
# 分页配置
# ---------------下面选项需要对应插件的支持---------------
# npm install hexo-generator-index --save
# npm install hexo-generator-archive --save
# npm install hexo-generator-category --save
# npm install hexo-generator-tag --save
## Set per_page to 0 to disable pagination
#每页显示的文章量
#per_page: 20
#首页的分页设置
index_generator:
  per_page: 5
#归档页的分页设置
archive_generator:
  per_page: 30
  yearly: true
  monthly: true
#标签页的分页设置
tag_generator:
  per_page: 20
#分页路径，在public中可以看到
#pagination_dir: page
# Extensions 拓展插件配置
## Plugins: https://hexo.io/plugins/
plugins:
baidusitemap:
  path: baidusitemap.xml
# 配置RSS
feed:
  #feed 类型 (atom/rss2)
  type: atom
  #rss 路径
  path: atom.xm
  #在 rss 中最多生成的文章数(0显示所有)
  limit: 0
# 自定义站点内容搜索
# 需要先安装插件：
# npm install hexo-generator-search --save
search:
  path: search.xml
  # 如只想索引文章，可设置为post
  field: all
# 主题配置
## Themes: https://hexo.io/themes/
#theme: false #禁用主题
#theme: landscape
theme: next
# 部署配置
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repository: git@github.com:name/name.github.io.git # 该配置后续说明
  branch: master
```


本文参考：
1.[https://www.jianshu.com/p/0823e387c019](https://www.jianshu.com/p/0823e387c019)

