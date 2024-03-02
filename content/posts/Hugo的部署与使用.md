---
title: "Hugo的部署与使用"
date: 2023-07-17T01:01:22+08:00
draft: false
tags: ["Hugo"]
categories: ["博客"]
---

# 1. 简介

Hugo 是一个使用 Go 编写的静态网站生成器，很适合用来部署个人博客网站。

官网：https://gohugo.io/

# 2. 部署

## 2.1 在mac部署

安装 Hugo：

```bash
brew install hugo
```

创建站点：

```bash
mkdir hugo
cd hugo
hugo new site huanglianjing.com
cd huanglianjing.com
```

打开 Hugo 的主题列表挑选主题：https://themes.gohugo.io/

下载并设置主题，将会将主题文件下载到 themes 文件夹内：

```bash
git init
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
git submodule add https://github.com/CaiJimmy/hugo-theme-stack.git themes/stack
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/papermod
git submodule add https://github.com/Vimux/Mainroad.git themes/mainroad

echo "theme = 'papermod'" >> hugo.toml
```

添加文章，将会在 content/posts 下创建文件，然后在创建的 markdown 文件的开头文本后面追加博文内容：

```bash
hugo new posts/xxx.md

# 将已有内容追加到markdown文件中
cat xxx.md >> content/posts/xxx.md
```

创建的文章默认是草稿，取消草稿只需在文章开头配置信息修改 draft 为 false。

网站配置：

```bash
vi hugo.toml
```

构建静态网站，并启动网站，网站启动后按 Ctrl + C 将会停止运行 Hugo：

```bash
# 不包含草稿
hugo server

# 包含草稿
hugo server -D

# 后台运行
nohup hugo server & >> nohup.log 2>&1
```

Hugo 启动默认端口号为1313，浏览器访问网站：

```
http://localhost:1313/
```

在 GitHub 上创建对应仓库，然后把本地仓库同步到 GitHub 上去：

```bash
git remote add origin https://github.com/huanglianjing/huanglianjing.com.git
git push -u origin master
```

## 2.2 在云主机部署

从 GitHub 上拉取仓库。

```bash
git clone https://github.com/huanglianjing/huanglianjing.com.git
```

如果网络没有反应，则可以从本地压缩整个文件夹，通过rz上传上去再解压。

```bash
# 本机执行
tar zcf huanglianjing.com.tgz huanglianjing.com/

# 云主机执行
rz
tar zxf huanglianjing.com.tgz
```

安装 Hugo：

```bash
# 打开 https://github.com/gohugoio/hugo/releases 选择最新版本的对应平台版本

wget https://github.com/gohugoio/hugo/releases/download/v0.115.3/hugo_0.115.3_Linux-64bit.tar.gz
tar zxf hugo_0.115.3_Linux-64bit.tar.gz
mv hugo /usr/bin/
```

在网站仓库对应的文件夹，构建网站，网站将会保存在 public 文件夹中：

```bash
hugo --baseURL "/"
```

启动 nginx 并调整配置：

```bash
service nginx start

vi /etc/nginx/nginx.conf
# user: 改为 root
# http - server - root: 改为 public 文件夹对应路径

nginx -t
nginx -s reload
```

然后在浏览器打开网址 http://huanglianjing.com/，成功看到内容！！！

# 3. 命令

查看 Hugo 版本：

```bash
hugo version
```

查看命令帮助：

```bash
hugo help
hugo server --help
```

编译站点：

```bash
hugo
```

文档状态被定义在每个 markdown 文章的开头：

* title：标题
* draft：草稿，默认为草稿
* date：文档日期
* publishDate：发布日期
* expiryDate：过期日期

运行 Hugo：

```bash
# 构建网站
hugo

# 构建网站并运行
hugo server
```

运行 Hugo 选择的模式：

```bash
# --buildDrafts or -D
# --buildExpired or -E
# --buildFuture or -F
# --navigateToChanged 编辑内容时自动重定向网页
```

构建站点后，将会将站点发布到 public 文件夹，文件夹结构如下：

```
public/
├── categories/
│   ├── index.html
│   └── index.xml  <-- RSS feed for this section
├── post/
│   ├── my-first-post/
│   │   └── index.html
│   ├── index.html
│   └── index.xml  <-- RSS feed for this section
├── tags/
│   ├── index.html
│   └── index.xml  <-- RSS feed for this section
├── index.html
├── index.xml      <-- RSS feed for the site
└── sitemap.xml
```

# 4. 文件结构

在站点的文件夹中，文件结构如下：

```
sitename/
├── archetypes/
│   └── default.md // hugo new 创建文档的默认开头内容模版
├── assets/        // 记录需要被处理的文件
├── content/       // 网站的内容
├── data/          // 生成网站的配置文件，格式如json/toml/yaml
├── layouts/       // 网站的html模板文件
├── public/
├── static/        // 静态内容，如图片、CSS、JavaScript等
├── themes/        // 存放主题文件
└── hugo.toml
```

Hugo 支持的配置文件格式包括 hugo.toml hugo.yaml hugo.json，可以指定配置文件构建网站：

```bash
hugo --config a.toml
```

自 Hugo v0.110.0 开始默认配置文件从 config.toml 改为了 hugo.toml。

# 5. 参考

* [Hugo](https://gohugo.io/)

