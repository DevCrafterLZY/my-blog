---
title: 关闭MySQL和MongoDB 的开机自启
published: 2024-12-13
description: "关闭MySQL和MongoDB 的开机自启"
tags: ["MySQL", "MongoDB"]
category: Guides
draft: false
---

在 Windows 上，关闭 MySQL 和 MongoDB 的开机自启可以通过以下步骤进行：

### 1. 关闭 MySQL 的开机自启
1. 打开 **“运行”**（按下 `Win + R`），输入 `services.msc`，按回车打开服务管理器。
2. 在服务列表中找到 **MySQL**（如果你使用的是 XAMPP 或其他安装包，可能显示为 `MySQL` 或 `mysql57` 等）。
3. 右键点击 MySQL 服务，选择 **“属性”**。
4. 在 **“启动类型”** 下拉框中选择 **“禁用”**。
5. 点击 **“确定”** 保存设置。

### 2. 关闭 MongoDB 的开机自启
1. 打开 **“运行”**（按下 `Win + R`），输入 `services.msc`，按回车打开服务管理器。
2. 在服务列表中找到 **MongoDB**（如果你是通过安装包安装 MongoDB，通常会显示为 `MongoDB`）。
3. 右键点击 MongoDB 服务，选择 **“属性”**。
4. 在 **“启动类型”** 下拉框中选择 **“禁用”**。
5. 点击 **“确定”** 保存设置。

这样设置后，MySQL 和 MongoDB 就不会在每次开机时自动启动了。如果需要重新启用开机自启，只需将 **“启动类型”** 设置为 **“自动”** 即可。