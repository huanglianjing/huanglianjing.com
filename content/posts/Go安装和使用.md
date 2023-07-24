---
title: "Go安装和使用"
date: 2023-07-25T01:51:34+08:00
draft: false
tags: ["Go"]
categories: ["Go"]
---

# 1. 安装

## 1.1 CentOS安装Go

```bash
$ yum install golang
```

## 1.2 macOS安装Go

macOS 可以通过 brew 安装 Go，需要先安装 brew。

```bash
$ brew install go
```



# 2. 工具

Go的工具链通过go命令配合子命令使用。

常用命令：

```
build      编译包和依赖
clean      删除目标文件
doc        显示文档
env        显示go环境变量
fmt        格式化代码
get        下载安装包和依赖
install    编译安装包和依赖
list       列出包
run        编译运行程序
test       测试包
version    显示go版本信息
vet        运行工具vet
```

执行工具：

```bash
$ go [commant] // 执行工具
$ go help [commant] // 查看工具文档
```

## 2.1 env

显示Go环境变量。

```bash
$ go env # 显示所有Go环境变量
$ go env GOPATH # 显示某个环境变量

$ go env -w GO111MODULE=on # 设置Go环境变量
$ go env -u GOPROXY # 取消env配置
```

**GOPATH**

环境变量 GOPATH 表示工作空间的根目录，其中有如下子目录：

```
GOPATH/
    src/  源文件
    bin/  可执行程序
    pkg/  编译的包
```

**GOROOT**

环境变量 GOROOT 指定Go发行版的根目录，也就安Go安装的目录，其中提供所有标准库的包。

## 2.2 version

显示Go版本。

```bash
$ go version
```

## 2.3 run

将一个或多个.go为后缀的源文件进行编译、链接，然后运行生成的可执行文件。

```bash
$ go run helloworld.go
$ go run helloworld.go one two three # 带有运行参数
```

## 2.4 build

将源文件编译输出成一个可执行的程序，该可执行文件可以直接执行。

```bash
$ go build helloworld.go
$ ./helloworld
```

## 2.5 clean

删除生成的对象文件和可执行文件。

```bash
$ go clean
```

## 2.6 test

运行测试。

```bash
$ go test
```

## 2.7 list

列出可用的包。

```bash
$ go list
$ go list java... # 使用...作通配符匹配子串
```

## 2.8 get

下载依赖包，将会被放到 GOPATH/pkg 里，目前支持从 Github、BitBucket、Google Code、Launchpad 等代码管理平台获取远程代码包。

```bash
$ go get github.com/golang/lint/golint

# 选项
# -d 只下载不安装
# -u 更新包和它的依赖包
# -f 带有-u时生效，不需要验证import的每一个包都获取了
# -t 同时下载运行测试所需的包
# -v 显示执行的命令
```

## 2.9 install

编译运行依赖包。

```bash
$ go install [packages]
```

## 2.10 fmt

运行 gofmt 进行代码格式化。

```bash
$ go fmt # 格式化当前目录下所有go文件，不包含子目录内的
$ go fmt <file> # 格式化某个go文件

# 选项
# -n 显示要执行的命令而不实际执行
```

go fmt 实际执行的可执行程序是 gofmt，在不指定具体文件时，是会对每个要执行的go文件调用 gofmt的。以下是 gofmt 的用法和参数：

```bash
$ gofmt <file> # 对go文件格式化

# 选项
# -l        将不符合格式化规范的源码文件绝对路径打印到标准输出，标准输出的go文件名就是执行了格式化的，其他未输出的就是不需要执行格式化的go文件
# -w        将格式化的内容写入文件
# -s        简化文件的代码
# -d        只把改写前后的对比内容打印到标准输出
# -e        打印所有语法错误到标准输出
# -comments 是否保留注释，默认隐式使用，默认值为true
# -tabwidth 设置缩进的空格数量，默认值为8
# -tabs     是否使用\t代表空格表示缩进，默认隐式使用，默认值为true
```

# 3. 参考

- [《Go程序设计语言》](https://book.douban.com/subject/27044219/)

