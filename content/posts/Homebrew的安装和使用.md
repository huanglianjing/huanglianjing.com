---
title: "Homebrew的安装和使用"
date: 2023-07-21T02:37:29+08:00
draft: false
showToc: true # 显示目录
TocOpen: true # 自动展开目录
categories: ["macOS"]
tags: ["Homebrew"]
---

# 1. 简介

Homebrew 是 macOS 上的一个软件包管理工具，能很方便地安装很多软件包。

官方网站：https://brew.sh/

Homebrew 安装软件时，会先将安装包下载到指定目录，对于非 root 用户会保存在 ~/Library/Caches/Homebrew，对于 root 用户会保存在 /Library/Caches/Homebrew。

然后会将软件安装在 /usr/local/Cellar/ 目录下，再将可执行文件以软链接文件的方式保存在 /usr/local/bin/ 目录下，该目录属于默认 $PATH，所以安装软件后直接输入软件名称就可以执行命令。

# 2. 安装与配置

安装 Homebrew 只需要执行以下命令，将会获取到安装脚本然后执行：

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

默认源访问速度较慢，替换为中科大源：

```bash
# homebrew目录
cd "$(brew --repo)"
git remote -v
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
git remote -v

# core目录
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote -v
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
git remote -v
```

# 3. 使用命令

更新 Homebrew 至最新：

```bash
brew update
```

支持的所有软件列表：

```bash
brew formulae
```

搜索软件，支持模糊搜索：

```bash
brew search <TEXT|/REGEX/>
```

查看已安装的软件：

```bash
brew list
brew list <package>
```

查看软件信息：

```bash
brew info <package>
```

安装软件：

```bash
brew install <package>
```

更新软件：

```bash
brew upgrade <package>
```

卸载软件：

```bash
brew uninstall <package>
```

