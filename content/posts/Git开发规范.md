---
title: "Git开发规范"
date: 2023-09-13T19:53:37+08:00
draft: false
showToc: true # 显示目录
TocOpen: true # 自动展开目录
categories: ["版本控制"]
tags: ["Git"]
---

# 1. 简介

Git 作为分布式的版本控制系统，支持对代码仓库的多人协作的开发与提交，这就需要约定分支开发、提交时的注释格式等规范，以便于多人协作开发的流程更加流畅，避免各种冲突情况的发生，每次代码提交的信息更加清晰明确。

# 2. 分支管理规范

在实际的开发中，我们往往需要多位开发人员对一个代码仓库进行协同开发，并且需要部署不同的环境，比如个人开发环境、测试环境、预发布环境、正式环境。在这个背景下，需要规范的分支管理以及开发规范。

如下图所示，代码仓库固定名称的分支有 master、release 和 test，分别对应正式环境、预发布环境、测试环境的代码分支。而 feature 与 hotfix 分支的具体名称，则应当在实际功能开发或者缺陷修复的时候，根据实际情况来命名。

![git_branch_standard](https://article-1304941664.cos.ap-guangzhou.myqcloud.com/version_control/git_branch_standard.png)

当我们需要开发某个功能的时候，基于 master 创建一个 feature 分支，然后在这个分支上提交代码，用于本地开发环境的测试。本地开发完成后，将 feature 分支合并至 test 分支，部署到测试环境，提供给测试人员测试。测试过程中发现的其他问题可以继续在 feature 分支提交并继续合并 test 分支。测试完成后，将 feature 分支合并至 release 分支，部署到预发布环境。预发布环境测试出现问题可以重复之前的提交代码以及合并分支的流程，没有问题则将 release 分支合并至 master 分支，并打上一个 tag，基于 tag 部署到正式环境。

而当线上遇到程序问题需要紧急修复时，从 master 分支创建一个 bugfix 分支，修复后依次合并 test 分支、release 分支、master 分支并验证修复。

# 3. tag命名规范

tag 的版本一般从 v1.0.0 开始，三个数字分别表示大的功能、小的功能、修复和调整。

添加后缀：

```
正式release版： v1.2.3
release候选版： v1.2.3-rc
内测版： v1.2.3-aplha
公测版： v1.2.3-beta
```

# 4. 提交说明规范

在一次代码提交时，应当写提交说明，描述这次提交的内容。

提交的说明建议包含类别和说明，如 `feat: 增加编辑功能` 表示这个提交是一个功能提交，提交的改动在于增加编辑功能。

类别的取值如下：

```
feat: 新功能
fix: 修复缺陷
docs: 文档改动
style: 格式调整，不改动功能
refactor: 重构，代码重构，但不改动功能
test: 增加测试
revert: 回滚提交
chore: 构建过程或辅助工具的变动
```

说明部分直接描述这次提交的内容，对于一些需求管理系统可以在说明中添加描述：

```
对应需求id: --story=需求id 需求说明
对应缺陷id: --bug=缺陷id 修复说明
测试: --test=说明
其它: --other=说明
```

# 5. 参考

* [Commit message 和 Change log 编写指南 - 阮一峰的网络日志](https://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)