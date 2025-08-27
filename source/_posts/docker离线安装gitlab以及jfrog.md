---
tags:
  - docker
categories:
  - ops
author: zmu
top: false
cover: /images/default-cover.jpg
toc: true
comments: true
date: 2025-07-02 16:55:00
updated: 2025-07-02 16:55:00
title: Dcoker离线安装Gitlab以及Jfrog
---

# 一、Linux离线配置docker

## 1.1 下载docker安装包

（1）下载地址
下载地址：https://download.docker.com/linux/static/stable/x86_64/
阿里云镜像网站：https://mirrors.aliyun.com/docker-ce/linux/static/stable/x86_64/

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/docker离线安装gitlab以及jfrog/image-20250702161519617.png" style=" margin: 0 15px 10px 0;float: left" />

（2）解压与拷贝
解压并把文件复制到/usr/bin目录下：

```bash
tar -zxvf docker-28.1.0.tgz
cp docker/* /usr/bin/
```

## 1.2 注册docker服务

在/etc/systemd/system/这个目录下下创建docker.service文件

```bash
vim /etc/systemd/system/docker.service
```

将如下内容拷贝进去

```bash
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target

[Service]
Type=notify
ExecStart=/usr/bin/dockerd
ExecReload=/bin/kill -s HUP $MAINPID
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
Delegate=yes
KillMode=process
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s

[Install]
WantedBy=multi-user.target
```

## 1.3 启动docker

```bash
# 给docker.service赋权限
chmod +x /etc/systemd/system/docker.service
# 重新加载systemd程序的配置文件
systemctl daemon-reload
#启动docker服务
systemctl start docker
# 使docker开机自启
systemctl enable docker
# 查看docker服务状态
systemctl status docker
```

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/docker离线安装gitlab以及jfrog/image-20250702161641829.png" style=" margin: 0 15px 10px 0;float: left" />

## 1.4 修改docker配置文件

docker安装后默认没有daemon.json这个配置文件，需要进行手动创建
1、配置镜像加速

```bash
vim /etc/docker/daemon.json
```

```json
{
    "registry-mirrors": [
        "https://docker.m.daocloud.io",
        "https://dockerproxy.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://docker.nju.edu.cn"
    ]
}
```

2、重启docker服务

```bash
systemctl daemon-reload
systemctl restart docker
```

4、查看docker版本，正常打印版本即安装成功

```bash
docker -v
```

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/docker离线安装gitlab以及jfrog/image-20250702161750754.png" style=" margin: 0 15px 10px 0;float: left" />



# 二、docker离线安装gitlab

## 2.1 下载gitlab镜像

先在可以联网的linux上下载gitlab镜像

```bash
# gitlab-ce为稳定版本，后面不填写版本则默认pull最新latest版本
docker pull gitlab/gitlab-ce
# 或指定版本
docker pull gitlab/gitlab-ce:17.5.0-ce.0
# 查看镜像
docker images 
```

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/docker离线安装gitlab以及jfrog/image-20250702153841956.png" style="width: 60%; display: block;margin: 0 15px 10px 0;" />
保存镜像

```bash
# docker save -o gitlab.tar gitlab/gitlab-ce
docker save -o gitlab.tar gitlab/gitlab-ce:17.7.0-ce.0
```

执行完命令，你会在当前目录获得一个gitlab.tar包

## 2.2 docker导入镜像

把下载的gitlab.tar压缩包拷到离线的linux机器上

```bash
# 执行命令，导入镜像
docker load -i gitlab.tar
# 查看镜像
docker images
```

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/docker离线安装gitlab以及jfrog/image-20250702153841956.png" style="width: 60%; display: block;margin: 0 15px 10px 0;" />
导入成功

## 2.3 docker启动gitlab容器并修改配置文件

（1）创建gitlab的配置、数据、日志目录，防止数据丢失，便于问题配查

 创建目录，这个目录可以自己更改

```haskell
mkdir -p /data/gitlab/config 
mkdir -p /data/gitlab/logs 
mkdir -p /data/gitlab/data
```

（2）启动docker容器

```bash
docker run -d -it 
-p 10010:10010         
-p 8013:22         
--name  gitlab    
--privileged  
-v /data/gitlab/config:/etc/gitlab         
-v /data/gitlab/logs:/var/log/gitlab         
-v /data/gitlab/data:/var/opt/gitlab         
-v /data/gitlab/logs/reconfigure:/var/log/gitlab/reconfigure         
gitlab/gitlab-ce:17.7.0-ce.0
```

查看容器状态

```bash
docker ps -a
```

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/docker离线安装gitlab以及jfrog/image-20250702154608890.png" style=" margin: 0 15px 10px 0;float: left" />

（3）容器启动之后，编辑/data/gitlab/config/gitlab.rb文件

```bash
#添加下面3行
external_url 'http://127.0.0.1:10010'
gitlab_rails['gitlab_ssh_host'] = '127.0.0.1'
gitlab_rails['gitlab_shell_ssh_port'] = 8013
```

然后进入容器

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/docker离线安装gitlab以及jfrog/image-20250702154802450.png" style=" margin: 0 15px 10px 0;float: left" />

```bash
# 执行更新配置操作
gitlab-ctl reconfigure
gitlab-ctl start
# 执行更新配置操作后退出容器
exit
```

（4）重启gitlab

```bash
docker restart gitlab
```

（5）登录gitlab

待gitlab启动后，访问http://192.168.88.175:10010

```bash
# 账号 root
# 密码通过指令查询，里面有一个Password的字符串就是root账户对应的密码
cat /data/gitlab/config/initial_root_password
```

## 2.4 修改管理员root密码

**登录root用户=>点击用户头像=>Edit profile=>Password** 

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/docker离线安装gitlab以及jfrog/image-20250702155859246.png" style=" margin: 0 15px 10px 0;float: left" />

## 2.5 GitLab中文界面设置

**登录root用户=>点击用户头像=>Preferences** 
在Preferences页面中，找到“Localization”区域，将“Language”选项修改为“简体中文”。
点击页面下方的“Save changes”按钮，保存语言设置。

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/docker离线安装gitlab以及jfrog/image-20250702160237214.png" style=" margin: 0 15px 10px 0;float: left" />



# 三、docker离线安装jfrog-artifactory

## 3.1 下载jfrog镜像

先在可以联网的linux上下载jfrog镜像

```bash
# 拉取镜像
docker pull docker.bintray.io/jfrog/artifactory-oss:latest
# 查看镜像
docker images
```

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/docker离线安装gitlab以及jfrog/image-20250702153841956.png" style="width: 60%; display: block;margin: 0 15px 10px 0;" />

保存镜像

```bash
docker save -o docker.bintray.io/jfrog/artifactory-oss:latest
```

执行完命令，你会在当前目录获得一个jfrog.tar包

## 3.2 docker导入镜像

把下载的jfrog.tar压缩包拷到离线的linux机器上

```bash
# 执行命令，导入镜像
docker load -i jfrog.tar
# 查看镜像
docker images
```

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/docker离线安装gitlab以及jfrog/image-20250702153841956.png" style="width: 60%; display: block;margin: 0 15px 10px 0;" />
导入成功

## 3.3 docker启动jfrog容器并修改配置文件

（1）设置jfrog数据文件夹权限，jfrog默认使用1030用户启动，将权限赋给1030

```bash
chown -R 1030:1030 /data/jfrog/data
chmod -R 775 /data/jfrog/data
```

（2）关闭防火墙，关闭selinux

```bash
# 关闭防火墙
sytemctl stop firewalld
# 关闭Selinux
vi /etc/selinux/config
```

```bash
# config
SELINUX=disabled
```

（3）启动docker容器

```bash
docker run -d
--name jfrog-artifactory 
-v /data/jfrog/data:/var/opt/jfrog/artifactory 
-p 8081:8081 -p 8082:8082 
docker.bintray.io/jfrog/artifactory-oss:latest
```

查看容器状态

```bash
docker ps -a
```

（4）登录jfrog-artifactory

待jfrog启动后，访问http://192.168.88.175:8082

```bash
# 账号 admin
# 密码 password
```
