---
title: Git clone来自Github上的仓库，报端口443错误
date: 2023-09-06 15:17:16
tags:
    - Git
categories: 编程经验
---
<meta name="referrer" content="no-referrer"/>

### 一、问题描述：
    克隆github上的开源项目报错：Failed to connect to github.com port 443 : Timed out
    如图所示：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/29411486/1693980752087-6c873ec7-54ab-449a-8664-fe70cf0cef8c.png#averageHue=%23312e2d&clientId=ue76d7c67-d5e7-4&from=paste&height=128&id=u520ece08&originHeight=128&originWidth=1129&originalType=binary&ratio=1&rotation=0&showTitle=false&size=33970&status=done&style=none&taskId=u72a6f70d-d096-4c76-9189-c3acb81b3d2&title=&width=1129)
### 二、原因：
    可能是git 所设端口与系统代理不一致，需重新设置。
### 三、解决方案：
##### 打开windows设置 -> 网络 -> 代理
![image.png](https://cdn.nlark.com/yuque/0/2023/png/29411486/1693980915514-6e9c670b-fa61-40e1-ae17-8200c2e3d594.png#averageHue=%232c1f2d&clientId=ue76d7c67-d5e7-4&from=paste&height=499&id=u32f3088a&originHeight=709&originWidth=848&originalType=binary&ratio=1&rotation=0&showTitle=false&size=77552&status=done&style=stroke&taskId=u39180eb5-3383-41eb-a639-8cbb44bc6fd&title=&width=597)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/29411486/1693980927551-209d9dd2-b4ba-4740-8f10-2395960c9d6b.png#averageHue=%23292428&clientId=ue76d7c67-d5e7-4&from=paste&height=518&id=ufc16de84&originHeight=660&originWidth=783&originalType=binary&ratio=1&rotation=0&showTitle=false&size=66751&status=done&style=stroke&taskId=uc10f872e-1dbb-46d2-8e8b-65c3b988e39&title=&width=614)
##### 记录下当前系统代理的IP地址和端口号。
    如上图所示，地址与端口号为：127.0.0.1:7890
##### 修改git的网络设置
```plsql
    # 注意修改成自己的代理地址
    git config --global http.proxy http://127.0.0.1:7890 
    git config --global https.proxy http://127.0.0.1:7890
```
重新clone 成功！
