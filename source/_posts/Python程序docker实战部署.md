---
title: Python程序docker实战部署
date: 2023-09-03 19:55:04
tags:
    - python
    - docker
categories: 架构
---

本文介绍python程序如何制作成docker镜像，并部署在线上服务器上。
### 一、python基础认知
#### 1. 包
在一个文件夹中存在一个__init__.py文件，python就会认为这个文件夹是一个包
可以在__init__.py文件中做一些初始化的配置、操作等
#### 2. 程序执行与主函数
Python是一种解释型编程语言。
Python程序没有显式的"main()"函数入口点。
在Python中，程序的执行从文件的顶部开始，逐行执行直到文件的末尾。
#### 3. Flask应用-web服务
Flask是Python编程语言中的一个轻量级Web应用框架。它被设计成简单、易用、灵活的框架，用于快速开发Web应用程序和API。
语法示例：
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000) # 0.0.0.0表示支持任意host

```
### 二、docker镜像制作
编写完python程序后 确认程序可执行。就可以制作docker镜像了
##### 1. requirements.txt文件
在Python中，requirements.txt是一个文本文件，通常用于列出项目所依赖的第三方Python包及其版本信息。
这个文件是为了方便项目的依赖管理而创建的。如：
```python
# 应用所需的Python依赖库
Flask==2.3.2
mysql-connector==2.2.9
psycopg2-binary==2.9.6
```
##### 2. Dockerfile文件
在项目的根目录新建Dockerfile文件如下，注意Dockerfile没有文件后缀名
```python
# 使用轻量级的基础镜像 打出的镜像包才不会很大
# 官方的完整版镜像包打出来都是1G的大小
FROM python:3.9-alpine

# 设置工作目录
WORKDIR /app

# 安装依赖库
RUN pip install --no-cache-dir --upgrade pip

# 将应用代码复制到镜像中 .表示当前目录 注意清理当前目录的不需要的文件 特别是大文件
COPY . /app

# 安装应用的依赖 注意变更requirements.txt的文件位置
RUN pip install --no-cache-dir -r ./resource/requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple

# 设置环境变量
ENV MYSQL_HOST 127.0.0.1
ENV MYSQL_PORT 3306
ENV MYSQL_USER root
ENV MYSQL_PASSWORD root
ENV MYSQL_DATABASE data_base

# 暴露Flask应用的端口
EXPOSE 5000

# 启动Flask应用
ENTRYPOINT ["python3", "-u", "./project/run.py","runserver","0.0.0.0:5000", "--noreload"]
```

##### 3. 镜像制作
在根目录下执行以下命令（docker环境下）
其中docker.xxx.cn为私服的域名，如果没有可以不写 即 test-demo:1.0.0；这里不写就是默认下载官方的
注意最后一个 . 是不能少的 
>  docker build -f Dockerfile -t docker.xxx.cn/test-demo:1.0.0 

### 三、部署
##### 1. 网络私服部署
将本地镜像上传到私服
> docker push docker.xxx.cn/test-demo:1.0.0

在目的服务器下载镜像
> docker pull docker.xxx.cn/test-demo:1.0.0

执行 docker run 命令启动容器
##### 2. 手动上传部署
将本地镜像打包成压缩包
> docker save -o test-demo.tar test-demo:1.0.0

然后将压缩包传到服务器中，执行加载命令
> docker load -i test-demo.tar

执行 docker run 命令启动容器
