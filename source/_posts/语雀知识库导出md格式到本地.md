---
title: 语雀知识库导出md格式到本地
date: 2023-02-08 06:12:56
tags: 其他
---


#### 1. 获取语雀个人开发者Token
访问：<https://www.yuque.com/yuque/developer/api#785a3731>
#### 2. 安装nodeJs
#### 3. 导出

```shell
    # 以下命令为Windows系统操作
    
    # 安装node依赖
    npm i -g yuque-exporter --registry=https://registry.npmmirror.com

    # 设置token环境变量
    set YUQUE_TOKEN=<your_token>

    # 导出全部知识库到当前文件夹
    yuque-exporter <个人账号，如:chenxin-uxuze>
```
