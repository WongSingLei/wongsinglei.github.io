---
title: GitHub Page + Hexo 搭建个人博客
date: 2018-12-12
tags: 配置
categories: 博客
---
# 前言
Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成**静态**网页。
# 一、博客搭建
## 1、配置Github
登录自己的Github账号，新建一个仓库，并命名为 `用户名.github.io`
## 2、配置环境
**2.1. 安装Git（如已安装可跳过），下载[Git](https://git-scm.com/download/win)并安装。**
安装成功后，鼠标右击打开Git Bash（或者在菜单里搜索Git Bash），设置`user.name`和`user.email`配置信息：
```
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的GitHub注册邮箱"
```
生成ssh密钥文件：
```
ssh-keygen -t rsa -C "你的GitHub注册邮箱"
```
然后直接三个回车即可，默认不需要设置密码。
找到生成的`.ssh`的文件夹中的`id_rsa.pub`密钥，将内容全部复制
打开[GitHub_Settings_keys](https://github.com/settings/keys) 页面，新建**new SSH Key**
Title为标题，任意填即可，将刚刚复制的`id_rsa.pub`内容粘贴进去，最后点击`Add SSH key`。
在Git Bash中检测GitHub公钥设置是否成功，输入：
```
ssh git@github.com
```
>设置GitHub密钥原因是，通过非对称加密的公钥与私钥来完成加密，公钥放置在GitHub上，私钥放置在自己的电脑里。GitHub要求每次推送代码都是合法用户，所以每次推送都需要输入账号密码验证推送用户是否是合法用户。为了省去每次输入密码的步骤，采用了ssh，当你推送的时候，git就会匹配你的私钥跟GitHub上面的公钥是否是配对的，若是匹配就认为你是合法用户，则允许推送。这样可以保证每次的推送都是正确合法的。

**2.2. 安装Node.js（如已安装可跳过），下载[Node.js](https://nodejs.org/en/download/)并安装。**
安装后，检测Node.js是否安装成功，在命令行中输入 `node -v`查看Node.js版本号。
检测npm是否安装成功，在命令行中输入`npm -v`查看npm版本号。
至此，安装Hexo的环境已经全部搭建完成。

**2.3. 安装Hexo**
Hexo就是我们的个人博客网站的框架，这里需要自己在电脑常里创建一个文件夹，可以命名为Blog，Hexo框架与以后你自己发布的网页都在这个文件夹中。创建好后，在命令行中将路径改为该文件夹（当然也可以在随意目录下，之后会在该目录下创建博客根目录）。
使用npm命令安装Hexo，输入：
```
npm install -g hexo-cli 
```
安装完成后，初始化我们的博客，输入：
```
hexo init blog
```
命令执行后会创建文件夹blog，之后更改路径为blog。
## 3、部署网站
安装Git部署插件
```
npm install hexo-deployer-git --save
```
打开站点目录下的配置文件`_config.yml`，翻到最后修改为(注意:最后三行冒号后面需要一个空格)
```
deploy:
  type: git
  repo: https://github.com/username/username.github.io
  branch: master
```
渲染生成和部署
```
hexo clean  //清除缓存，网页正常情况下可以忽略此命令
hexo g      //generate生成
hexo d      //deploy部署发布
```
## 4、查看效果
1. 直接打开自己的博客网站，因为延迟的存在可能不能看到最近的修改。
2. 在cmd中输入 **hexo server**，接着在浏览器输入**http://localhost:4000/**

# 二、博客备份
>备份原因：部署上去的，即发布到 GitHub 的都是编译后的文件，如 `html、css，js`等文件，但是自己写的`.md` 文章实际是没有上传至 GitHub 的，如果需要备份这些源文件，该怎么备份呢？或者换了别的电脑该怎么更新博客呢？

一般来说，存在两种博客备份方案：新建一个分支；新建另一个仓库。
将博客源文件hos在GitHub上后，发布流程就会变为：
1. 执行git push把更新的源文件push到托管源文件的GitHub Repo (称之为Source Repo)
2. 执行hexo deploy来更新托管静态网站的GitHub Pages (称之为Content Repo)
   
## 新建一个分支
>
>在原有仓库新建分支，网上有些办法是新建一个Repo，再建两个分支
### 创建分支目录
先新建一个文件夹，作为分支的工作目录，用于保存将要备份的文件。
再把GitHub上的Hexo仓库clone到hexo文件夹中
删除除了.git文件夹的其他所有文件，主要是为了得到版本管理的.git。下面命令不会删除隐藏文件和文件夹
~~~
rm -r *
~~~
最后把需要备份的文件和文件夹都复制到hexo文件夹下，hexo的目录结构应该如下
~~~
scaffolds/
source/
themes/
.git/
.gitignore
_config.yml
package.json
~~~

#### 创建分支
创建一个分支hexo
~~~
git checkout -b hexo
~~~
保存所有文件到暂存区
~~~
git add -all
~~~
提交变更
~~~
git commit -m "创建hexo分支"
~~~
推送到GitHub，并与origin创建关联，将hexo设置为默认分支
~~~
git push --set-upstream origin hexo
~~~
移除themes目录下的Git管理文件`.git/`，否则无法将主题文件夹push

在hexo文件夹下执行`npm install`和`npm install hexo-deployer-git`

### 发表文章
新建Markdown文件并编辑
~~~
hexo new <file>
~~~
将相关更改推送到hexo分支
~~~
git add .
git commit -m "发表文章"
git push origin hexo
~~~
将静态文件推送到master分支
~~~
hexo g
hexo d
~~~
也可以使用`hexo g -d`一步搞定。

### 迁移
将hexo分支clone下来
~~~
git clone -b hexo <HTTP/SSH>
~~~
然后安装Hexo
~~~
npm install
npm install hexo-deployer-git
~~~

参考：
- https://zhuanlan.zhihu.com/p/26625249
- https://hexo.io/zh-cn/
- https://www.makcyun.top/hexo02.html