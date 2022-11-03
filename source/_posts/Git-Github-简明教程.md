---
title: Git Github 简明教程
date: 2017-01-11 17:05:19
tags: [Git,Github]
categories: 技术
---

***
*本文大量参考于：[Git教程-廖雪峰](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)*
***
# Git是分布式版本控制系统
适用于纯文本文件txt、html等，不适用于word文件、图片、视频等
### 常用的cmd命令
<!--more-->
```
//在当前目录下创建新的子文件夹
  mkdir <文件夹名>
  mkdir new //建立名为“new”的文件夹  

//进入当前目录下该路径中的文件夹
  cd <路径>
  cd coder\hexo //进入当前目录下coder文件夹的子文件夹hexo中

//显示当前目录
  pwd

//列出当前目录下的所有项目（文件夹及文件）
  ls

//显示文件内容
  cat file
  cat new.txt //显示new.txt的内容
//创建新文件
  cat > filename  
  cat > new2.txt //创建new2.txt文件

//删除文件
  rm filename
  rm new2.txt //删除new2.txt文件
```

### 当前目录git化
（需要的话注意用cd命令切换目录）
```
git init //当前目录git化
```

### 将一个文件放到Git仓库
```
//第一步：添加文件
  git add file
  git add new.txt //添加文件到Git仓库

//第二步：提交本次修改并命名此次修改
  git commit -m "name of this change"
  git commit -m "wrote a new txt" //提交此次修改（即添加new.txt）并命名此次修改为“wrote a new txt”

//可以添加多个文件后再提交
```

### 工作区、暂存区

![（此处盗图，原图来自廖雪峰）](http://upload-images.jianshu.io/upload_images/3984866-9810933040355de8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
工作区（Working Directory）即电脑中能看到的git化目录
版本库（Repository）即 .git，包括：
* 暂存区（stage或index），用add将修改添加到此处
* 分支 （第一个分支是master），用commit将修改提交到当前分支
* HEAD指针

### 查询当前仓库状态
```
//查询当前仓库状态，是否有文件被修改，该修改是否提交，常用该命令随时掌握工作区状态
  git status

//查询当前仓库状态与最后一次提交修改时的状态有何具体的不同，文件具体哪一内容被修改
  git diff
```


### 撤销文件修改
```
//撤销某文件在工作区的全部修改，即某文件回到最后一次提交add或commit的版本
  git checkout -- file
  git checkout -- new.txt //将new.txt回到最后一次提交add或commit的版本

//撤销某文件在暂存区的修改并将其放回到工作区（之后如果需要可以再用上面的命令撤销工作区修改）
  git reset HEAD file
  git reset HEAD new.txt //将暂存区中new.txt的修改放回到工作区中
```

### 删除文件
可以理解为`git add`的相反操作
```
//用 git status 查询到某个文件被删之后，可以删除版本库中的文件（在这之后需用git commit提交才行）
  git rm file // 将之前在工作区中的删除修改放到暂存区中
  git rm new.txt  
```

### 版本回退
```
//查询提交日志，能显示所有的提交日志
  git log //显示commit id（版本号）、提交人、日期、该次修改的名称
  git log --pretty=online //只显示commit id（版本号）、该次修改的名称

//查询命令日志，能显示所有操作过的命令
  git reflog //显示操作命令后的版本号及命令内容

//重置（回退或前进）到某个版本
//HEAD表示当前版本，HEAD^表示上个版本，HEAD^^表示上上个版本，HEAD~100表示往上100个版本
  git reset --hard 版本 //重置为某个版本
  git reset --hard HEAD^^ //回退至上上个版本
  git reset --hard  df5258b //重置为commit-id为df5258b的版本
```


### 创建标签 操作标签
标签是指向对应commit的指针
```
//默认在最新提交的commit上创建标签
  git tag 标签名
  git tag v1.0

//在对应版本号(commit id)的commit上创建标签
  git tag 标签名 版本号
  git tag v1.0 df5258b

//创建带有说明的标签
  git tag -a 标签名 -m "说明" 版本号
  git tag -a v1.0 -m "我是第一版标签的说明" df5258b

//查看所有标签(按字母排序)
  git tag

//查看标签信息
  git tag 标签名
  git tag v1.0

//创建的标签值存储在本地，不会自动推送

//推送某个标签
  git push origin 标签名
  git push origin v1.0 //推送v1.0标签到远程仓库

//一次性推送所有未推送的本地标签
  git push origin --tags

//删除本地标签
  git tag -d 标签名
  git tag -d v1.0 //删除本地的v1.0标签

//删除远程标签，先删除本地标签然后推送删除命令
  git push origin :refs/tags/标签名
  git tag -d v1.0 //删除本地的v1.0标签
  git push origin :refs/tags/v1.0 //推送删除远程仓库v1.0标签的命令



```
***
# GitHub是托管Git仓库的网站
### 创建SSH key密钥对
```
//创建SSH key，每台电脑的用户(C:/User)主目录下会生成.shh目录，包含id_rsa(私钥，不能泄露)和id_rsa.pub(公钥)
  ssh-keygen -t rsa -C "youremail@example.com" //这之后一路回车使用默认值即可
```
### 添加远程仓库，并关联本地仓库与远程仓库
```
//注册并登陆GitHub
//Account setting--SSH Keys
//Add SSH Key，在Key中粘贴id_rsa.pub文件中的内容
//create repository 创建一个仓库，【不勾选】initialize this repository with a README
//在本地仓库目录中运行下面命令，以SHH方式关联本地仓库与远程仓库，其中origin是远程仓库在本地仓库中的昵称，可以改成其他的
  git remote add origin git@github.com:用户名/仓库名.git

//以上方法采用SHH方式关联(个人认为适用于受信任的私人电脑)，也可以通过https方式(适用于公用的或不被信任的电脑)，不过https方式速度较慢且每次push均须输入GitHub用户名及密码，命令如下
  git remote add origin https://github.com/用户名/仓库名.git
```
以SHH方式关联后，首次push或clone会收到SSH key确认的警告，核对后输入yes即可，以后不再出现

### 推送本地仓库内容到远程仓库
```
//首次推送，需关联本地仓库master分支与远程仓库master分支
  git push -u origin master

//正常推送，将本地提交的修改推送到远程仓库
  git push origin master
```

### 远程仓库克隆到本地
```
//在GitHub中create repository 创建一个仓库，【勾选】initialize this repository with a README
//克隆远程仓库
  git clone git@github.com:用户名/仓库名.git //SHH方式
  git clone https://github.com/用户名/仓库名.git //https方式
```

### 分支管理（日后补充）
### 自定义git（日后补充）
* 忽略特殊文件
* 配置别名
* 搭建Git服务器  


***
