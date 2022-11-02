---
title: Hexo与GitHub Pages搭建博客
date: 2017-01-11 19:44:54
tags: Hexo
categories: 技术
---
***
近期用Hexo和GitHub Pages搭建了自己的博客，走了一些弯路，为防止自己以后忘记，现记录下来

搭建前深情地补习了一下git知识并做了笔记
[Git教程-廖雪峰](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
[笔记-Git Github 简明教程](https://swibinchter.github.io/2017/01/11/Git-Github-%E7%AE%80%E6%98%8E%E6%95%99%E7%A8%8B/)

***
## 具体步骤
<!--more-->
1. 安装git for windows
[git for windows下载入口](https://git-for-windows.github.io/)

2. 安装node.js
[node.js下载入口](https://nodejs.org/en/download/)

3. 安装Hexo
```
// -g为全局参数
npm install -g hexo-cli
```

4. 创建Hexo文件夹Blog并初始化
```
//hexo init 文件夹名
hexo init Blog
```
至此本地的Hexo博客已经搞定，可以使用下面的命令
```
//创建新文章
hexo new "文章名字" //可简写为 hexo n "文章名字"
//启动服务器在http://localhost:4000/中预览博客效果
hexo server //可简写为 hexo s
```
5. 以SSH key方式授予本机修改GitHub的权限
```
//创建本机SSH key，每台电脑的用户(C:/User)主目录下会生成.shh目录，包含id_rsa(私钥，不能泄露)和id_rsa.pub(公钥)
  ssh-keygen -t rsa -C "youremail@example.com" //这之后一路回车使用默认值即可
//注册并登陆GitHub
//Account setting--SSH Keys
//Add SSH Key，在Key中粘贴id_rsa.pub文件中的内容
```

6. 关联GitHub Pages
 * 在GitHub上新建仓库，命名为你的GitHub用户名.github.io，例如 jack.github.io
 * 打开Blog目录下的_config.yml文件，将最后三行修改如下：
```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
      type: git
      repo: git@github.com:你的GitHub用户名/你的GitHub用户名.github.io
      branch: master
```
*注：此处是以SSH方式关联，也可以不使用SSH key而用Https方式，不过每次链接均须输入GitHub账户及密码，虽然安全但不方便。*
 * 安装hexo-deployer-git插件
```
//-S等同于--save，可以将安装信息保存到package.json文件中
npm install hexo-deployer-git -S
```
7. 将本地Hexo博客内容部署(deploy)到GitHub上
```
//生成静态文件
hexo generate //可简写为 hexo g
//部署到GitHub
hexo deploy //可简写为 hexo d
```
8. 在GitHub Pages上查看，地址为：  
https://你的GitHub用户名.github.io/  
例如 https://jack.github.io

9. 打完收工

***
## 日常操作
创建新文章
```
hexo n
```
文章编辑完成后在本地查看
```
hexo s
```
部署到GitHub Pages上
```
hexo g
hexo d
```

***
