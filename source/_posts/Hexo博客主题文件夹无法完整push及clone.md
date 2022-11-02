---
title: Hexo博客主题文件夹无法完整push及clone
date: 2017-01-11 19:31:35
tags: [Hexo,Hexo theme]
categories: 技术
---
***
## 情景描述
搭建完hexo博客之后，想要把源代码备份或者想在其他终端上使用，其中一种方法便是push到GitHub上，需要时clone下来，然而themes下的主题文件夹除了默认主题比较正常之外，其他主题文件夹push到GitHub上之后却是灰色，将其clone之后发现是空文件夹，辛辛苦苦配置的主题难道需要重新配置一遍！？  
***
## 解决思路
删除这三项使Yilia变回普通目录——重新提交推送
<!--more-->
因为这种主题一般是`git clone`而来的，其本身就是一个git仓库，只要删除该主题下的相关git文件即可
以我使用的hexo-theme-yilia为例，从主题作者litten那里`git clone`之后THemes/Yilia文件夹是这样的

![`git clone`之后的Themes/Yilia文件夹](http://upload-images.jianshu.io/upload_images/3984866-ba400ddde5a1928b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到选中的三项，依次是：
* .git文件夹：代表此目录本身是一个git仓库
* .gitignore：忽略你不想提交到git仓库的内容
* .gitattributes：设置非文本文件(如word等)的对比方式  

而这个git仓库关联的是主题作者的远程仓库，故而无法提交到本地git仓库中从而无法成功推送到自己的github上

***
## 具体步骤：
*注：未提交过主题文件到git仓库的朋友无视前4步，直接从第5步开始*
1. 将Yilia文件夹剪切到git仓库之外的目录（剪切可以防止自己的主题配置丢失）

2. 提交从而删除本地git仓库之中的Yilia
```
git add .
git commit`
```

3. 推送从而删除远程仓库(gihub)之中的Yilia
```
git push
```
此时登陆github查看themes文件夹，发现之前灰色的Yilia无效文件夹已经没了

4. 将第1步剪切出去的Yilia的文件夹复制回Themes文件夹里

5. 删除.git文件夹、.gitignore、.gitattributes这三项（经过实践发现删除.git文件夹一项即可，但其他两项已然无用，一并删除罢了）

6. 提交从而增加本地git仓库之中的Yilia
```
git add .
git commit`
```

7. 推送从而增加远程仓库(gihub)之中的Yilia
```
git push
```
此时登陆github查看themes文件夹，发现Yilia文件夹及其内容已经成功推送

8. 打完收工
