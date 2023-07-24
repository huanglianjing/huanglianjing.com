---
title: "Go标准库：context"
date: 2023-07-25T01:51:33+08:00
draft: false
tags: ["Go"]
categories: ["Go"]
---

# 1. 介绍

文档地址：https://pkg.go.dev/context

Go 标准库 context 用于在函数调用、API调用、进程调用间传递上下文。

Go官方建议不要将 context 定义在其他结构体中，将 context 作为独立的参数传递给需要的函数且应该是首个参数，变量名一般使用 ctx。示例如下：

```go
func DoSomething(ctx context.Context, arg Arg) error {
	// ... use ctx ...
}
```

context 在多个协程中同时使用是安全的。

# 2. 类型

## 2.1 Context

类型定义：

```go
// Context 上下文
type Context interface {
	Deadline() (deadline time.Time, ok bool) // 被取消的时间
	Done() <-chan struct{} // 返回一个通道，context被取消时关闭
	Err() error // Done未关闭时返回nil，关闭时返回关闭原因
	Value(key any) any // 获取key对应的val
}
```

方法：

```go
// Background 返回一个空的context，不会被取消，没有值，没有deadline，通常用于主函数、初始化、测试、最上层接收请求时
func Background() Context

// TODO 返回一个空的context，不确定会用到context时使用
func TODO() Context

// WithValue 返回父context的复制，并关联key-val
func WithValue(parent Context, key, val any) Context
```

## 2.2 CancelCauseFunc

类型定义：

```go
type CancelCauseFunc func(cause error)
```

## 2.3 CancelFunc

类型定义：

```go
type CancelFunc func()
```

# 3. 函数

```go
// Cause 返回解释为什么context被取消
func Cause(c Context) error

// WithCancel 返回一个父context的复制，父context取消或者cancel被调用都会取消当前context
func WithCancel(parent Context) (ctx Context, cancel CancelFunc)
func WithCancelCause(parent Context) (ctx Context, cancel CancelCauseFunc)

// WithTimeout 带有过期时间
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
```

