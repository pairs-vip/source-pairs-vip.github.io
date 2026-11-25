---
title: Arthas 生产排查 SOP
date: 2026-09-16 11:46:59
tags:
  - Java
  - 排查
categories:
  - dev
author: pairs
top: false
cover: /images/default-cover.jpg
toc: true
comments: true
---

# Arthas 生产排查 SOP（标准流程 + 命令模板）

## 1. 目的

- 快速定位生产环境慢接口、耗时方法
- 分析 SQL、RPC、代码逻辑耗时
- 安全、可控地进行线上 Java 服务诊断

---

# 2. Arthas 部署

## 2.1 启动

```bash
java -jar arthas-boot.jar
```

选择目标 JVM 进程进行 attach。

---

## 2.2 离线部署

生产环境建议使用完整包：

```
arthas-bin.zip
```

解压：

```
arthas/
├── arthas-boot.jar
├── arthas-agent.jar
├── arthas-spy.jar
└── as.sh
```

启动：

```bash
./as.sh
```

避免运行时联网下载依赖。

---

# 3. 慢接口排查流程

## Step 1：查看方法签名

```bash
sm com.example.Controller
```

确认方法参数。

---

## Step 2：查看源码

```bash
jad com.example.Controller
```

定位：

- Controller 调用了哪个 Service
- 是否存在循环、远程调用、数据库查询

---

## Step 3：trace 调用链

Controller：

```bash
trace com.example.Controller methodName
```

Service：

```bash
trace com.example.Service methodName
```

MyBatis：

```bash
trace org.apache.ibatis.executor.BaseExecutor query
```

作用：

定位具体耗时方法。

---

## Step 4：watch 查看参数和耗时

```bash
watch com.example.Controller methodName '{params,returnObj}' '#cost'
```

查看：

- 方法参数
- 返回结果
- 执行耗时

---

## Step 5：tt 调用快照

开启：

```bash
tt -t com.example.Controller methodName
```

查看：

```bash
tt -l
```

查看详情：

```bash
tt -i <index>
```

手动清空快照：

```bash
tt --delete-all
tt -d -i <index>
```

注意：

tt 不是：

```
tt 类 方法 参数类型
```

这种写法。

---

# 4. CPU / GC 排查

## CPU 高

```bash
thread -n 10
```

查看热点线程。

查看阻塞线程：

```bash
thread -b
```

---

## GC

```bash
jstat -gc <PID> 1000
```

---

# 5. Arthas 生命周期管理（重要）

## 5.1 停止当前 trace/watch

执行：

```
Ctrl + C
```

作用：

- 停止当前分析命令
- 不关闭 Arthas

---

## 5.2 退出 Arthas 客户端

```bash
quit
```

或者：

```bash
exit
```

作用：

- 退出当前控制台
- 不一定释放目标 JVM 中的 Arthas Server

---

## 5.3 完全关闭 Arthas

排查结束后重新连接目标 JVM：

```bash
java -jar arthas-boot.jar
```

进入 Arthas 后执行：(版本不同命令可能不同)

```bash
stop/shutdown
```

作用：

- 关闭 Arthas Server
- 卸载 Arthas
- 释放 Arthas 占用端口

---

## 5.4 端口冲突处理

如果出现：

```
Address already in use
```

通常原因：

之前 Arthas 没有关闭。

处理：

1. 重新 attach 原 Java 进程：

```bash
java -jar arthas-boot.jar
```

2. 执行：(版本不同命令可能不同)

```bash
stop/shutdown
```

3. 再重新启动 Arthas。

---

## 推荐结束流程

排查完成：

```
Ctrl+C
    ↓
stop/shutdown
    ↓
退出服务器
```

不要只执行：

```
quit
```

否则可能留下 Arthas Server，占用端口。

---

# 6. Arthas Tunnel Server

用于远程诊断：

```
本地 Arthas
      |
      |
Tunnel Server
      |
      |
生产 JVM
```

适合：

- 内网服务器
- 无法直接访问 JVM
- 多机器管理

启动：

```bash
java -jar arthas-tunnel-server.jar
```

---

# 7. 常用命令汇总

```bash
# 查看方法
sm com.example.Controller

# 查看源码
jad com.example.Controller

# 调用链耗时
trace com.example.Controller methodName

# 查看参数和耗时
watch com.example.Controller methodName '{params,returnObj}' '#cost'

# 调用记录
tt -t com.example.Controller methodName

# 停止当前命令
Ctrl+C

# 关闭 Arthas
shutdown/stop

# 退出客户端
quit
```

---

# 8. 排查口诀

先 jad 看源码

↓

trace 找耗时

↓

watch 看参数

↓

tt 做快照

↓

stop/shutdown清理 Arthas
