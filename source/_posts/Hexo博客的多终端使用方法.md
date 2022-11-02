---
title: Hexo博客的多终端使用方法
date: 2017-01-11 19:42:23
tags: Hexo
categories: 技术
---
***
## 情景描述
搭建完hexo博客难免会遇到这样的情况：
1. 旧电脑淘汰，换新电脑，然而博客还得继续...
2. 家里一台电脑，办公室一台电脑，随身一台笔记本，偶尔去去网咖，换工作又一台办公室的电脑.....多台电脑，然而博客无时无刻都想写..  
***
## 解决思路
A电脑搭建Hexo博客——A电脑必要源文件push到github仓库中——B电脑配置git、node环境——B电脑从github上clone必要源文件——B电脑安装Hexo  
<!--more-->
Hexo的多终端使用或备份关键在于必要源文件的一致性，将其放到github并接受各个终端的推送，需要时clone到新终端上配置一下hexo即可
>我博客的必要源文件如下：
>scaffolds文件夹——存放模板文件
>source文件夹——存放md文件
>themes文件夹——存放主题文件（须删除其中非默认主题文件夹里的`.git`开头文件夹及文件，否则无法成功>push，详参见：[Hexo博客主题文件夹无法完整push及clone](https://swibinchter.github.io/2017/01/11/Hexo%E5%8D%9A%E5%AE%A2%E4%B8%BB%E9%A2%98%E6%96%87%E4%BB%B6%E5%A4%B9%E6%97%A0%E6%B3%95%E5%AE%8C%E6%95%B4push%E5%8F%8Aclone/)）
>.gitignore文件——git时忽略的内容
>_config.yml文件——bolg的配置
>package.json文件——blog的依赖、插件等  

![我的必要源文件](http://upload-images.jianshu.io/upload_images/3984866-fcb5e2948dca8ace.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

*注：本文是直接在github上新建仓库将必要源文件放入其中，还可以在GitHubPages对应仓库建立其他分支将其放入其中，会显得统一一些。有兴趣的朋友可以根据本文最下方的参考链接研究一番*
***
## 具体步骤
1.  在A电脑文件夹Blog中搭建博客，配置主题，部署到GitHubPages等

2. A电脑通过配置gitignore的内容来避免push非必要源文件(默认的gitignore内容随hexo版本而异)
我的gitignore文件内容如下(可以直接复制替换):
```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
*~
```

3. A电脑检查themes文件夹的每个子文件夹，删掉所有以`.git`开头的文件及文件夹
我的主题是Yilia，需删除.git文件夹、.gitignore、.gitattributes这三项
这一步是为了能让主题文件夹能成功push到github上，详参见：[Hexo博客主题文件夹无法完整push及clone](https://swibinchter.github.io/2017/01/11/Hexo%E5%8D%9A%E5%AE%A2%E4%B8%BB%E9%A2%98%E6%96%87%E4%BB%B6%E5%A4%B9%E6%97%A0%E6%B3%95%E5%AE%8C%E6%95%B4push%E5%8F%8Aclone/)

4. 在GitHub上新建仓库Blog

5. 本地Blog目录git化并推送到远程Blog（Github）仓库
```
//Blog目录git化
git init
//添加必要文件到git仓库
git add .
//提交
git commit -m "xxx"
//本地Blog仓库关联远程Blog（Github）仓库
git remote add origin git@github.com:你的github用户名/Blog.git
//建立分支追踪
git push -u master
//此时所有必要文件已全部push到了GitHub上的Blog仓库中
```

6. B电脑安装Git、node环境

7. B电脑clone远程远程Blog（Github）仓库
```
//克隆必要文件
git clone add origin git@github.com:你的github用户名/Blog.git
```

8. 在B电脑Blog目录中配置Hexo
```
//安装hexo
npm install hexo
//安装依赖
npm install
```

9. 测试
```
//启动服务器，进入http://localhost:4000/查看，已正常显示
hexo s
//生成静态文件
hexo g
//部署到GitHubPages
hexo d
//打开https://你的github用户名.github.io/查看，已正常显示
```

10. 打完收工

***
## 日常使用
1. 从GitHub上更新必要源文件
```
git pull
```
2. 在本地或写文章，或修改配置，或换主题
*注：需要注意的是换主题时一定要删除其中的以`.git`开头的文件*
3. 将必要源文件更新到GitHub上
```
git add .
git commit -m "xxx"
git push
```
4. 部署到GitHub Pages上
```
hexo g
hexo d
```
***
## 问题汇总
**Q1**： A电脑能正常部署的情况下，B电脑无法`hexo d`部署到GitHub Pages？
**A**：
1. 检查B电脑是否安装hexo-deployer-git依赖，执行代码`npm install hexo-deployer-git --save`
2. 检查_config.yml文件中deploy配置信息是否正确，应如下所示：
```
//注意冒号之后有一个空格
  deploy:
     type: git
     repo: https://github.com/leopardpan/leopardpan.github.io.git**
     branch: master
```

**Q2**：除了默认主题之外的主题无法完整`git push`到github上?
**A**：因为这种主题一般是`git clone`而来的，其本身就是一个关联着主题作者的git仓库，只要删除该主题下的相关git文件即可，具体参见我的另一篇文章：

***
## 链接
* 参考
[GitHub Pages + Hexo搭建博客 第四节](http://crazymilk.github.io/2015/12/28/GitHub-Pages-Hexo%E6%90%AD%E5%BB%BA%E5%8D%9A%E5%AE%A2/#more)
[Hexo 换终端/换电脑小记](http://wangmuduo.com/2016/04/02/hexo-change-os/)
* 扩展
[Hexo博客主题文件夹无法完整push及clone](https://swibinchter.github.io/2017/01/11/Hexo%E5%8D%9A%E5%AE%A2%E4%B8%BB%E9%A2%98%E6%96%87%E4%BB%B6%E5%A4%B9%E6%97%A0%E6%B3%95%E5%AE%8C%E6%95%B4push%E5%8F%8Aclone/)
