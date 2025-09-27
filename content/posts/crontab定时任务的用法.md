---
title: "crontab定时任务的用法"
date: 2024-11-03T10:03:50+08:00
draft: false
showToc: true # 显示目录
TocOpen: true # 自动展开目录
categories: ["Linux"]
tags: ["Linux","crontab"]
---

crontab 是 Linux 中用于管理和执行定时任务的工具，支持分钟粒度的命令执行。

操作系统会默认安装此服务工具，并启动 crond 进程，每分钟定期检查是否有需要执行的任务，然后自动执行。

Linux 的任务调度分为系统任务调度和用户任务调度。

**crond 任务**

```bash
# 查看服务状态
systemctl status crond

# 开启服务
systemctl start crond

# 关闭服务
systemctl stop crond

# 重启服务
systemctl restart crond

# 重新载入配置
systemctl reload crond
```

**系统任务调度**

系统任务调度保存在 /etc/crontab 中，需要打开进行编辑然后保存。

**用户任务调度**

用户任务调度保存在 /var/spool/cron/crontabs/ 下，每个用户区分一个文本文件。

可以使用 crontab 命令编辑和查看每个用户的定时任务。

选项：

- -e 编辑用户的定时任务
- -l 列出用户的定时任务
- -r 删除用户的定时任务
- -u \<user\> 指定用户，需要带有其他选项

**配置**

一个定时任务的配置分为以下几段：

```
minute hour day month week command
```

分别表示：

* minute：分钟，可以是 0-59
* hour：小时，可以是 0-23
* day：日期，可以是 1-31
* month：月份，可以是 1-12
* week：星期几，可以是 0-7，0 和 7 表示星期日
* command：要执行的命令或脚本

以上时间字短可以使用的特殊字符：

* 星号(*)：表示所有值，如分钟的 * 表示每分钟
* 横杠(-)：表示整数范围，如 0-9 表示 0 到 9 的数字范围
* 斜杠(/)：指定时间频率，如 */2 表示偶数范围，1/2 表示奇数范围
* 逗号(,)：隔开多个范围值，如 10,30,50 表示匹配三个值，0-5,*/10 表示两端范围的合集

**实例**

```bash
# 每分钟执行一次
* * * * * command

# 在上午8点到11点的第3和15分钟执行
3,15 8-11 * * * command

# 每个月1日的1点开始的每4小时执行
* 1/4 1 * * command
```

