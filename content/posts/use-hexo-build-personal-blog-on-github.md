---
title: 使用Hexo在GitHub上搭建个人博客
date: 2016-05-03 09:26:17
categories: 动手做
tags: 
- hexo
- github
- blog
---
# 本机环境
* 系统：Ubuntu 16.04 LTS
* Hexo:3.2.0
* NodeJS:v5.11.0
* Git:2.7.4

# 重要环境

1. 安装nodejs
2. 安装git
3. 注册github
4. 使用github搭建个人博客，详细参考：[GitHub pages](https://pages.github.com/)

<!--more-->

# 安装Hexo

	1. mkdir hexo #新建hexo目录
	2. cd hexo #进入hexo目录
	3. npm install -g hexo #如果不成功使用npm install -g hexo --registry=https://registry.npm.taobao.org
	4. hexo init #如果还是卡，不成功，请使用npm config set registry "https://registry.npm.taobao.org"（详细请搜索npm代理之类的文章，国内npm被墙，所以很慢，可使用阿里的npm镜像）
	5. hexo g #或者hexo generate
	6. hexo s #或者hexo server
	7. 使用http://localhost:4000查看是否启动成功

# 安装hexo主题

此处使用hexo-theme-next主题，详情：[hexo-theme-next](https://github.com/iissnan/hexo-theme-next)，如有需要可选择其他的主题。
	1. hexo clean
	2. git clone https://github.com/iissnan/hexo-theme-next themes/next

# 启用主题

修改hexo目录下的站点配置文件_config.yml，将theme: landscape修改为theme: next

# 查看主题

	1.hexo g
	2.hexo s #启动服务，输入http://127.0.0.1:4000查看
	3.启动成功之后，关于主题配置请查看：[主题配置](http://theme-next.iissnan.com/getting-started.html)

# 生成静态文件

	hexo generate

# 发布到github

1. 修改站点配置文件_config.yml,找到下面内容：
```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
type:
修改为：
deploy:
type: git
repository: https://github.com/你的用户名/你的用户名.github.com.git # 这是你自己的仓库地址，注意换成自己的用户名
branch: master
```
2. 执行hexo deploy
3. 查看http://dachengxi.github.com

# 写博客或添加页面

```
1. hexo new "postName" #新建文章
2. hexo new page "pageName" #新建页面
3. hexo generate #生成静态页面至public目录
4. hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
5. hexo deploy #将.deploy目录部署到GitHub
```

# 错误解决
	
# 参考

1. http://codepub.cn/2015/04/06/Github-Pages-personal-blog-from-Octopress-to-Hexo/
2. http://www.cnblogs.com/zhcncn/p/4097881.html

