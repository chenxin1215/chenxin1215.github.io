---
title: 搭建Hexo博客
date: 2023-09-03 20:49:13
tags:
sticky: 100
---

# hexo是什么
[Hexo](https://hexo.io/zh-cn/)是一个博客框架，使用markdown语法写作，一键编译生成静态文件，一键部署到github或gitlab等。
# 初始化hexo博客项目
首先安装node环境，然后全局安装 hexo-cli ：
```bash
cnpm install -g hexo-cli
```
安装成功后，使用 hexo init 命令初始化项目：
```bash
hexo init myblog  # 会在当前目录下创建一个myblog目录
```
# 创作文章
进入myblog目录，执行以下命令可以创建一篇新文章，标题为test：
```bash
hexo new test
```
hexo会在source/_posts目录下创建一个test.md文件，我们在这里使用markdown语法写博客即可：
```bash
---
title: test
date: 2023-09-02 21:17:39
tags: 
- hexo
- 博客
categories: 教程
---

# 一级标题

## 二级标题

这里开始我们的写作吧
```
上面文件最上方以---分隔的区域，用于指定文章属性（[Front-matter | Hexo](https://hexo.io/zh-cn/docs/front-matter)），常用的属性就是title, date, tag, categories，分别指定文章标题、建立日期、标签和分类。其中标题和建立日期都是自动生成的，我们不用管。tag和categories一般是由我们自己设置的。

**hexo中的分类categories和标签tag的区别在于：categories编译后对应的是一个分类目录，而tag只是一个标记：**

**一篇文章只能包含一个分类，但是可以包含多个标签。**
我们使用hexo new <文章名>命令创建的文章，是被放到了source/_posts目录下。事实上，post是hexo文章的一种布局，**hexo一共支持3种布局方式：post、page、draft**，使用下面的命令可以创建指定布局的文章：
```bash
hexo new post <文章名>
hexo new page <文章名>
hexo new draft <文章名>
```
其中post布局的文章会被创建到source/_posts目录下，表示这是要被发表的文章；page布局的文章会在source目录下创建一个以<文章名>命名的文件夹，在文件夹下再创建一个index.md，这是个啥意思我也不懂，但是我们一般不用它，因为我们已经使用了tag和categories，就没法使用page了；draft布局的文章会被创建到source/_drafts目录下，表示这是一个草稿，当使用hexo generate命令编译时，不会被编译成静态页面。
![img](image.png)
如果我们想预览草稿，可以在编译时添加--draft参数：hexo generate --draft。如果我们想把草稿发布，可以使用hexo publish <文章名>命令。
写完文章后，我们先使用hexo clean命令清除之前编译的站点页面。
然后使用hexo generate命令编译出静态站点页面（保存在source/public中）。
最后使用hexo server命令就可以在本地预览：
![img](image1.png)
访问http://localhost:4000：
![img](image2.png)
小提示：在hexo3中新增了hexo-server，支持hexo监视文件变动并自动更新，无需重启服务器：
```bash
cnpm install hexo-server --save
hexo server
```
# 部署到Github
hexo最牛逼的地方在于，可以把博客部署到github、gitlab、gitee、gitcode等git代码网站上，利用它们提供的pages服务提供博客服务。
这是因为hexo编译生成的是一个静态网站，而pages服务就是提供静态网站访问服务的。

1、创建一个github仓库，开通pages服务。
2、安装 [hexojs/hexo-deployer-git: Git deployer plugin for Hexo. (github.com)](https://github.com/hexojs/hexo-deployer-git)
```bash
cnpm install hexo-deployer-git --save
```
3、修改博客配置（_config.yml）:
```yaml
deploy:
  type: git
  repo: git@github.com:xxxx/xxxx.github.io.git
  branch: master
```
4、生成站点文件并推送至远程仓库：
```bash
hexo clean  # 保险起见，每次我们都清空一下
hexo deploy --generate
```
5、登入 Github/BitBucket/Gitlab，请在库设置（Repository Settings）中将默认分支设置为_config.yml配置中的分支名称。稍等片刻（github后台在使用Travis CI自动部署），您的站点就会显示在您的Github Pages中。
此外，如果您的 Github Pages 需要使用 CNAME 文件自定义域名，请将 CNAME 文件置于 source 目录下，只有这样 hexo deploy 才能将 CNAME 文件一并推送至部署分支。
更多参考：[部署 | Hexo](https://hexo.io/zh-cn/docs/one-command-deployment)