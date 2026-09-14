---
tags:
  - rabbitmq
  - 消息队列
  - 排查
categories:
  - dev
author: pairs
top: false
cover: /images/default-cover.jpg
toc: true
comments: true
date: 2026-04-24 15:06:00
updated: 2026-04-24 15:06:00
title: 查找rabbitMQ消费者服务
---

# 查找rabbitMQ消费者服务

碰到一个问题，生产者发出的消息被消费了，但是在我预期的消费者服务没有日志输出，多次尝试确实消费并没有被预期的消费者消费，而是被另一个服务消费了，但是生产环境起了很多服务，我不知道具体被谁消费了，通过jar包一个一个去查找也比较浪费时间，并且不一定能找到，现在提供一种方式，查找这个消费者服务。

##### 排查方式：

1.首先得知道这个队列的名称或交换机的名称，通过查看队列详情在rabbitMQ控制台找到消费者都有谁：

访问rabbitMQ控制台：192.168.10.223:15672  ，登录用户名密码后，Queues标签下找到要查询的队列名称，点进去查看详情

![rabbitMQ队列详情](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/rabbitmq/rabbitmq队列详情.png)

2.通过详情可以看到消费者都有谁，例如我这里只有一个消费者，此时消费者ip后面跟的端口不是服务端口，是消费者服务通道端口

![rabbitMQ消费者信息](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/rabbitmq/rabbitmq消费者信息.png)

3.接下来通过消费者通道端口查询消费者服务进程号

```shell
# 服务器执行指令
#方法1:使用netstat(CentOS/RHEL)
sudo netstat -tunp I grep 5672 I grep ESTABLISHED
#方法2:使用 ss(更推荐，Ubuntu/Debian)
sudo ss -tunp src :消费者通道端口 dst 你的RabbitMQ_IP:5672
# 例: sudo ss -tunp src :54321 dst 192.168.100.1:5672
```

4.最终通过pid进程号来查询服务

```shell
#查看进程名和启动命令(最常用)
ps -fp<PID>
#如果进程是Java应用，直接看JAR 包名
ps -p <PID> -o pid,cmd
```

