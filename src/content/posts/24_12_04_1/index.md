---
title: 防晒项目启动
published: 2024-12-04
description: "防晒项目启动"
tags: ["project configuration"]
category: Guides
draft: false
---

## 防晒项目启动

#### 1. 启动云服务器实例
注意端口是否开放
#### 2. 打开服务器vpn
- 打开vpn的ui web界面
```shell
cd /home/clash-for-linux-backup/
bash start.sh
```
在本机可以通过`http://<ip>:9090/ui`进入登录界面， 在`API Base URL`中填写`http://<ip>:9090/`
，在Secret(optional)中填写，启动时显示的在Secret。
- 加入环境变量
```shell
source /etc/profile.d/clash.sh
```
- 启动代理
```shell
proxy_on
```
tips: 关闭代理`proxy_off`
https://github.com/Elegycloud/clash-for-linux-backup
#### 3. 打开内网穿透
```shell
sudo systemctl restart cpolar
```
https://www.cpolar.com/docs
#### 4. 启动项目
```shell
conda activate chatR
cd /home/chatR/
nohup python app.py &
```