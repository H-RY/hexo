---
title: hexo + github 搭建个人博客
date: 2025-01-07 21:36:32
tags:
---

## Hexo + Github 搭建个人博客

### 环境搭建

下载 Node.js：https://nodejs.org/zh-cn
下载 Git：https://git-scm.com/downloads
下载完成后依次输入 node -v、npm -v 和 git --version 检验



### 连接Github

右键 -> Git Bash Here，设置用户名和邮箱

```git
git config --global user.name "GitHub 用户名"
git config --global user.email "GitHub 邮箱"
```

创建SSH密钥

```git
ssh-keygen -t rsa -C "GitHub 邮箱"  然后一路回车
```

添加密钥

```git
cd ~/.ssh
```

复制公钥id_rsa_pub

登陆 GitHub ，进入 Settings 页面，选择左边栏的 SSH and GPG keys，点击 New SSH key。

Title 随便取个名字，粘贴复制的 id_rsa.pub 内容到 Key 中，点击 Add SSH key 完成添加。



### 创建GithubPages仓库

GitHub 主页右上角加号 -> New repository

Repository name 中输入 用户名.github.io
勾选 “Initialize this repository with a README” Description 选填
填好后点击 Create repository 创建。
创建后默认自动启用 HTTPS，博客地址为：https://用户名.github.io

注意：必须是public，否则别人访问你的博客还需要权限



### 本地安装Hexo

新建一个文件夹用来存放 Hexo 的程序文件，如 Hexo-Blog。打开该文件夹

使用 npm 一键安装 Hexo 博客程序

```js
npm install -g hexo-cli
```


初始化并安装所需组件：

```js
hexo init      # 初始化
npm install    # 安装组件
```


完成后依次输入下面命令，启动本地服务器进行预览：

```js
hexo g   # 生成页面
hexo s   # 启动预览
```


Tips：如果出现页面加载不出来，可能是端口被占用了。Ctrl+C 关闭服务器，运行 hexo server -p 5000 更改端口号后重试。



### 部署 Hexo 到 Github Pages

安装 hexo-deployer-git

```js
npm install hexo-deployer-git --save
```



修改 _config.yml 文件末尾的 Deployment 部分

deploy:
  type: git
  repository: git@github.com:用户名/用户名.github.io.git
  branch: master

完成后运行 hexo d 将网站上传部署到 GitHub Pages即可

