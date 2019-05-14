---
title: GitHub Page + Hexo 搭建个人博客
tags: [配置]
categories: 博客
date: 2018-12-12 15:05:57
modified: 2019-04-02 23:07:33
---
# 前言
Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成**静态**网页。
# 一、博客搭建
## 1、配置 Github
登录自己的 Github 账号，新建一个仓库，并命名为 `username.github.io`
## 2、配置环境
**2.1. 安装 Git（如已安装可跳过），下载[Git](https://git-scm.com/download/win)并安装。**
安装成功后，鼠标右击打开 Git Bash（或者在菜单里搜索 Git Bash），设置`user.name`和`user.email`配置信息：
```
git config --global user.name "你的 GitHub 用户名"
git config --global user.email "你的 GitHub 注册邮箱"
```
生成ssh密钥文件：
```
ssh-keygen -t rsa -C "你的GitHub注册邮箱"
```
然后直接三个回车即可，默认不需要设置密码。
找到生成的`.ssh`的文件夹中的`id_rsa.pub`密钥，将内容全部复制
打开 [GitHub_Settings_keys](https://github.com/settings/keys) 页面，新建 **new SSH Key**
Title 为标题，任意填即可，将刚刚复制的`id_rsa.pub`内容粘贴进去，最后点击`Add SSH key`。
在 Git Bash 中检测 GitHub 公钥设置是否成功，输入：
```
ssh git@github.com
```
>设置 GitHub 密钥原因是，通过非对称加密的公钥与私钥来完成加密，公钥放置在 GitHub 上，私钥放置在自己的电脑里。GitHub 要求每次推送代码都是合法用户，所以每次推送都需要输入账号密码验证推送用户是否是合法用户。为了省去每次输入密码的步骤，采用了 ssh，当你推送的时候，git 就会匹配你的私钥跟 GitHub 上面的公钥是否是配对的，若是匹配就认为你是合法用户，则允许推送。这样可以保证每次的推送都是正确合法的。

**2.2. 安装 Node.js（如已安装可跳过），下载 [Node.js](https://nodejs.org/en/download/) 并安装。**
安装后，检测 Node.js 是否安装成功，在命令行中输入 `node -v`查看 Node.js 版本号。
检测 npm 是否安装成功，在命令行中输入`npm -v`查看npm版本号。
至此，安装 Hexo 的环境已经全部搭建完成。

**2.3. 安装 Hexo**
Hexo 就是个人博客网站的框架，这里需要自己在电脑常里创建一个文件夹，Hexo 框架与以后你自己发布的网页都在这个文件夹中。创建好后，在命令行中将路径改为该文件夹（当然也可以在随意目录下，之后会在该目录下创建博客根目录）。
使用 npm 命令安装 Hexo，输入：
```
npm install -g hexo-cli 
```
安装完成后，初始化博客，输入：
```
hexo init blog
```
命令执行后会创建文件夹 blog，之后更改路径为 blog。
## 3、部署网站
安装 Git 部署插件
```
npm install hexo-deployer-git --save
```
打开站点目录下的配置文件`_config.yml`，翻到最后修改为(注意：最后三行冒号后面需要一个空格)
```
deploy:
  type: git
  repo: https://github.com/username/username.github.io
  branch: master
```
渲染生成和部署
```
hexo clean  //清除缓存，网页正常情况下可以忽略此命令
hexo g      //generate 生成
hexo d      //deploy 部署发布
```
## 4、查看效果
1. 直接打开自己的博客网站，因为延迟也许不能即时看到最新的修改。
2. 在cmd中输入 `hexo server`(或`hexo s`)，接着在浏览器输入 **http://localhost:4000/** 即可及时查看修改效果。

# 二、博客备份
>备份原因：部署上去的，即发布到 GitHub 的都是编译后的文件，如 `html、css，js`等文件，但是自己写的`.md` 文章实际是没有上传至 GitHub 的，如果需要备份这些源文件，该怎么备份呢？或者换了别的电脑又该怎样更新博客呢？

一般来说，使用 GitHub 仓库备份博客，存在两种方案：新建一个分支；新建另一个仓库。因为笔者之前没有考虑到备份问题，因此已经在 GitHub 上创建了一个 Repo ，所以采用新建分支的方式。

将博客源文件 host 在 GitHub 上后，发布流程就会变为：
1. 执行 git push 把更新的源文件 push 到托管源文件的 GitHub Repo (称之为 Source Repo)
2. 执行 hexo deploy 来更新托管静态网站的 GitHub Pages (称之为 Content Repo)
   
## 新建一个分支（hexo）
>关于新建分支的方式，网上的另一种办法是新建一个 Repo，再建两个分支，再把 hexo 分支 clone 下来。然后把需要备份的文件和文件夹复制到 hexo 文件夹下。然而新建的 Repo 也必须命名为`username.github.io`，会与之前 Repo 冲突，所以还得改之前 Repo 的名字。
>笔者采用的是先 clone 已有 Repo：`username.github.io`，再删除不必要的文件（仅保留版本控制`.git`文件夹），然后把需要备份的文件和文件夹复制到 hexo 文件夹下。接着再建 hexo 分支。

### 创建分支目录
1. 先新建一个文件夹，作为分支的工作目录，用于保存将要备份的文件。
2. 再把 GitHub 上的 Hexo 仓库 clone 到当前文件夹中。
~~~
git clone <url> hexo
~~~
因为在 URL 后添加了 `hexo`，所以会创建 hexo 文件夹。当然不添加也行，这样创建的文件夹应该是 `username.git.io`。
3. 接着删除除了 .git 文件夹的其他所有文件
~~~
rm -r *
~~~
4. 最后把需要备份的文件和文件夹（scaffolds/，source/，themes/，.gitignore，
_config.yml，package.json）都复制到 hexo 文件夹下，复制后 hexo 的目录结构应该如下
~~~
scaffolds/
source/
themes/
.git/
.gitignore
_config.yml
package.json
~~~

#### 创建分支并配置环境
创建一个分支 hexo
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
推送到 GitHub，并与 origi n创建关联，将 hexo 设置为默认分支
~~~
git push --set-upstream origin hexo
~~~
移除 themes 目录下的 Git 管理文件`.git/`，否则无法将主题文件夹 push。

在hexo文件夹下执行`npm install`和`npm install hexo-deployer-git`。

### 发表文章
新建 Markdown 文件并编辑
~~~
hexo new <file>
~~~
将相关更改推送到 hexo 分支
~~~
git add .
git commit -m "发表文章"
git push origin hexo
~~~
将静态文件推送到 master 分支
~~~
hexo g
hexo d
~~~
也可以使用`hexo g -d`命令一步搞定。

### 迁移
将 hexo 分支 clone 下来
~~~
git clone -b hexo <HTTP/SSH>
~~~
然后安装 Hexo
~~~
npm install
npm install hexo-deployer-git
~~~

参考：
- https://zhuanlan.zhihu.com/p/26625249
- https://hexo.io/zh-cn/
- https://www.makcyun.top/hexo02.html