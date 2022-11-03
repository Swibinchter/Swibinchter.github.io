---
title: 博客搭建-Hexo与GitHub Pages
date: 2017-01-11 19:44:54
tags: Hexo
categories: 技术
---
***
## 前记

近期用Hexo和GitHub Pages搭建了自己的博客，做一个记录，也可以使用Gitee，弄懂原理后过程大同小异

<!--more-->

***

## 原理逻辑

* **GitHub**是一个基于git分布式版本管理的远程仓库平台，可以随时随地pull和push
* GitHub提供的**Pages**功能
  * 可以将仓库中的html、css、js等前端文件直接生成静态页面
  * 可以通过设置特定的仓库名后直接访问，无需自行解决服务器的问题，免费省心
* **Hexo**
  * 可以将写好的文章生成html、css、js等前端文件
  * 可以把生成的前端文件直接推送到github上
  * 主题丰富美观，让博客页面更加高大上
* **首次建立博客的过程：**
  1. GitHub建立特定名字的远程仓库
  2. Hexo在本地文件夹生成前端文件
  3. Hexo配置文件中关联GitHub仓库和分支，将前端文件推送到GitHub仓库中
  4. 使用GitHub Pages对应网址访问博客
* **多终端使用和源码备份：**
  1. **Hexo推送前端文件时只依据`_config.yml`配置文件中设置的远程仓库和分支**
  2. 远程仓库默认的`master`分支，用于接收和存放Hexo推送的前端文件
  3. 在远程仓库新建一个分支(如`blog_hexo_soure_code`)，用于接收和存放Hexo的源代码，并设置为仓库的默认分支
  4. 以后所有的终端首次使用时，从该仓库`clone`，直接就是默认分支，也就是存放Hexo的源代码的分支(如`blog_hexo_soure_code`)
  5. 非首次使用时，先`git pull`更新为当前最新的源代码
  6. 本地编辑完后推送完前端文件到`master`分支后，再将代码`git`+`add`/`commit`/`push`，更新远程仓库分支(如`blog_hexo_soure_code`)的源代码

* **后续使用博客的过程：**

    1. 在本地的Hexo文件夹中写文章
    2. Hexo在本地文件夹生成前端文件
    3. Hexo将前端文件推送到GitHub仓库中
    4. `git push`推送更新后的源代码修改到GitHub仓库中【非多终端使用，可以省略此步】
    5. 使用GitHub Pages对应网址访问博客





***
## 建立博客的具体步骤
### 一、<a id="environment">环境准备</a>

* 环境安装基本都是境外的源，大概率会很慢，可以在执行前调整为国内的源，陈列如下：
  * [清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/) 
  * [华为开源镜像站](https://mirrors.huaweicloud.com/home)
  * [阿里云开源镜像站](https://developer.aliyun.com/mirror/)

  ```shell
  //比如阿里云开源镜像站，先运行此命令后，然后再使用安装命令
  npm config set registry https://registry.npm.taobao.org
  ```

1. 安装Git for windows，Github必备
  [git for windows下载入口](https://git-for-windows.github.io/)

2. 安装Node.js，Hexo是基于node的，所以必须安装
  [node.js下载入口](https://nodejs.org/en/download/)

3. 安装Hexo

   * 安装命令可能会随最新版Hexo变化，建议提前看下[Hexo官方入口](https://hexo.io/zh-cn/) 

   ```shell
   # -g为全局参数
   npm install -g hexo-cli
   ```



### 二、GitHub中建立博客专用的远程仓库

* [GitHub官网入口](https://github.com/)

1. 登录后在GitHub上新建仓库，命名为`GitHub用户名.github.io`，例如 `swibinchter.github.io`，用户名记得修改为自己的

2. 到这一步已经可以通过HTTPS方式与GitHub仓库进行通信，但每次都需要输入用户名和密码，推荐使用SSH的方式进行通信

3. 以<a id="SSH">SSH key</a>方式授予本机修改GitHub的权限

   * 创建本机SSH key，默认在用户主目录(C:/User)下会生成.shh目录，包含

     * `id_rsa.pub`：公钥，要放到GitHub上用于识别身份
     * `id_rsa`：私钥，不能泄露

     ```shell
     ssh-keygen -t rsa -C "youremail@example.com"
     # 这之后一路回车使用默认值即可
     ```

   * 在GitHub中添加SSH key，位置在`Account` >>>> `Settings` >>>> `SSH Keys` >>>> `Add SSH Key` >>>> 在`Key`中粘贴`id_rsa.pub`文件中的内容



### 三、Hexo在本地文件夹生成前端文件 

1. 在本地电脑上新建文件夹，例如`Blog`

2. 使用Hexo初始化，将其变为一个Hexo文件夹

   ```shell
   hexo init
   # 对文件夹进行hexo初始化，添加必备文件，本质是从Hexo在GitHub的官方库中clone必备文件到本地
   ```

3. 初始化时已经添加一篇名为“hello world”的博文和对应的前端文件，可以直接在本地预览效果【此步可以省略】

   ```shell
   # 启动服务器，浏览器输入http://localhost:4000/中预览博客效果
   hexo s		# 简写
   hexo server  # 完整写法
   ```



### 四、Hexo关联GitHub远程仓库，推送前端文件

1. 安装Hexo的git推送插件`hexo-deployer-git`
    ```shell
    npm install hexo-deployer-git -S
    # -S等同于--save，可以将安装信息保存到package.json文件中
    ```
2. 打开本地文件夹(`Blog`)下的_config.yml配置文件，将最后三行修改如下：

    ```shell
    # 注意冒号之后有一个空格
    deploy:
          type: git
          repo: git@github.com:你的GitHub用户名/你的GitHub用户名.github.io
          branch: master
    
    # repo为hexo推送前端文件时对应的远程仓库地址，可以直接在GitHub页面复制，此处使用的SSH方式对应地址，比如git@github.com:Swibinchter/Swibinchter.github.io.git
    # brach为hexo推送前端文件时对应的远程仓库的分支，远程仓库默认只有master主分支
    ```

    * **敲黑板，重点来了！Hexo推送前端文件只看上述配置文件里写的仓库和分支，跟GitHub的其他分支或其他远程仓库没有任何关联**，理解这点，会容易明白后面讲到的博客源码备份和多设备操作
3. 使用Hexo推送前端文件到GitHub的远程仓库

    ```shell
    hexo d		# 简写
    hexo deploy  # 完整写法
    ```



### 五、使用GitHub Pages对应网址访问博客

1. 浏览器输入GitHub Pages对应网址进行查看

   ```shell
   # 地址格式
   https://你的GitHub用户名.github.io/
   # 例如
   https://swibinchter.github.io
   ```



***

## 博客的多终端使用和源代码备份

* **重点再说一次，Hexo推送前端文件只看`_config.yml`配置文件里设置的仓库和分支，跟GitHub的其他分支或其他远程仓库没有任何关联**

#### 一、源代码备份

1. 仅第一次需要，后续添加新终端时可忽略此部分

2. 在GitHub上新建仓库(`swibinchter.github.io`)时会默认创建`master`分支，Hexo的`_config.yml`配置文件中已设置该分支接收前端文件

3. 在GitHub的仓库(swibinchter.github.io)下新建一个分支(如`blog_hexo_soure_code`)，用于接收和存放Hexo的源代码，并设置为仓库的默认分支，位置在对应仓库的`Code` >>>> `Settings` >>>> `Branches` >>>> `Default branch`

4. 已经部署Hexo的第一个终端A上`clone`该仓库，默认分支就是上一步建立的存放Hexo的源代码的分支(如`blog_hexo_soure_code`)

   ```shell
   # SSH方式，需要提前将本地仓库与GitHub关联，将SSH公钥添加到GitHub中，A终端已经添加完毕
   git clone git@github.com:你的github用户名/仓库名.git
   # 例如
   git clone git@github.com:Swibinchter/Swibinchter.github.io.git
   ```

5. 进入`clone`的文件夹中，删除掉除了`.git`外所有的文件，将原博客所在的Hexo文件夹下所有文件复制进来

6. 检查themes文件夹的每个子文件夹，打开显示隐藏文件，删掉所有以`.git`开头的文件及文件夹，这一步是为了后续能让主题文件夹能成功推送，因为git不能嵌套上传

   ```shell
   比如主题Yilia，需删除.git文件夹、.gitignore、.gitattributes这三项
   ```

7. 推送到远程仓库中(默认就是存放Hexo的源代码的分支(如`blog_hexo_soure_code`))，建议先配置下终端A的用户名，方便跟其他终端区分，这样可以在GitHub上区分每次`commit`对应的终端

   ```shell
   # 设置终端A的用户名
   git config --global user.name “xxxx(这个终端的用户名)” 
   
   # 添加文件夹里所有文件修改到暂存区
   git add .
   # 提交add的修改到本地分支上
   git commit -m "第1次推送源代码的说明"
   # 推送本地分支的修改到远程仓库的对应分支上
   git push
   ```

8. 至此，GitHub上已完成所有部署，`master`分支用于接收和存放Hexo推送的前端文件，新建并设置为默认的分支(如`blog_hexo_soure_code`)用于接收和存放Hexo的源代码

#### 二、添加新终端

1. 新终端B安装必要的Git for windows、Node.js、Hexo，详见上文[环境准备](#environment)，配置一下这个终端上的用户名，便于跟其他终端做区分

   ```shell
   git config --global user.name “xxxx(这个终端的用户名)” 
   ```

2. 终端B上`clone`GitHub远程仓库，默认分支就是上一步建立的存放Hexo的源代码的分支(如`blog_hexo_soure_code`)

   * SSH方式(推荐)，需要提前将本地仓库与GitHub关联，将SSH公钥放到GitHub中，详见上文[SSH key设置](#SSH)

       ```shell
       git clone git@github.com:你的github用户名/仓库名.git
       # 例如
       git clone git@github.com:Swibinchter/Swibinchter.github.io.git
       ```

   * Http方式，可以直接进行，但需要输入GitHub账户密码

     ```shell
     git clone https://github.com/你的github用户名/仓库名.git
     ```

3. 在文件夹下安装npm各种插件依赖，需要安装的依赖取决于`packages.json `文件，安装插件时的`--save`/`-S`参数就是把插件信息保存到其中

   ```shell
   //安装依赖
   npm install
   //为防万一，安装hexo-deployer-git
   npm install hexo-deployer-git --save
   ```

4. 使用Hexo在本地验证下博客【此步可以省略】

   ```shell
   # 启动服务器，浏览器输入http://localhost:4000/中预览博客效果
   hexo s		# 简写
   hexo server  # 完整写法
   ```

5. 至此，终端B的博客已经配置完成，可以正常使用，需要注意的是，**多终端使用时，务必要先`git pull`获取GitHub仓库中最新的源代码后，再编辑文章-`hexo g`生成前端文件-`hexo d`推送前端文件，最后需要通过`git`+`add`/`commit`/`push`将源代码修改同步到GitHub仓库**



------

## 日常使用博客的具体步骤

1. 获取GitHub远程仓库对应的分支(`blog_hexo_soure_coder`)中最新的源代码【非多终端使用，可以省略此步】

   ```shell
   git pull
   ```

2. 直接右键新建，使用Hexo创建新文章，新文章文件会出现本地的source文件夹下

   ```
   hexo n "文章名字"	 # 简写
   hexo new "文章名字"   # 完整写法
   ```

3. 编辑新文章

4. 使用Hexo在本地预览博客页面效果【此步可以省略】

   ```shell
   # 启动服务器，浏览器输入http://localhost:4000/中预览博客效果
   hexo s		# 简写
   hexo server  # 完整写法
   ```

5. 使用Hexo生成前端文件

   ```
   hexo g		  # 简写
   hexo generate # 完整写法
   ```

6. 使用Hexo推送前端文件到GitHub的远程仓库对应的分支(`master`)中

   ```shell
   hexo d		# 简写
   hexo deploy  # 完整写法
   ```

7. 推送最新的源代码修改到GitHub的远程仓库对应的分支(`blog_hexo_soure_coder`)中【非多终端使用，可以省略此步】

   ```shell
   # 添加Hexo文件夹里所有文件修改到暂存区
   git add .
   # 提交add的修改到本地分支上
   git commit -m "本次修改的简要说明"
   # 推送本地分支的修改到远程仓库的对应分支上
   git push
   ```



***

## 问题汇总

**Q1**： A电脑能正常使用的情况下，B电脑无法`hexo d`推送到GitHub Pages？
**A**：

1. 检查B电脑是否安装`hexo-deployer-git`依赖，执行代码`npm install hexo-deployer-git --save`
2. 检查`_config.yml`文件中`deploy`配置信息是否正确，应如下所示：

    ```shell
    # 注意冒号之后有一个空格
      deploy:
         type: git
         repo: https://github.com/leopardpan/leopardpan.github.io.git
         branch: master
    ```



**Q2**：安装`hexo`和`hexo-deployer-git`巨慢无比?
**A**：国外的资源访问较慢，可以先设置使用国内镜像，然后再使用安装命令
```npm config set registry https://registry.npm.taobao.org```



**Q3**：按照上述步骤建立的博客页面，风格不喜欢?
**A**：默认主题并不好看，Hexo有丰富的主题和设置，参见其他文件更换主题即可



**Q4**：除了默认主题之外的主题无法完整`git push`到GitHub上?
**A**：因为这种主题一般是`git clone`而来的，其本身就是一个关联着主题作者的git仓库，只要删除该主题下的相关git文件即可



**Q5**：Hexo文件夹里都是弄啥的嘞?
**A**：Hexo 6.3.0主要文件/文件夹的说明

| 主要文件/文件夹 | 说明                                                         | 是否需要存到GitHub上 |
| :-------------- | :----------------------------------------------------------- | :------------------: |
| .deploy_git     | `hexo g`自动生成的                                           |        不需要        |
| node_modules    | Hexo需要的模块，就是一些基础的npm安装模块，比如一些美化插件，在执行`npm install`的时候会重新生成 |        不需要        |
| public          | `hexo g`命令执行后生成的静态页面文件                         |        不需要        |
| scaffolds       | 新建文章时的模板                                             |         需要         |
| source          | 用于存放文章的`.md`文件                                      |         需要         |
| themes          | 主题文件                                                     |         需要         |
| .gitignore      | Hexo生成的， 用来忽略一些文件，表示这些类型文件不需要git     |         需要         |
| _config.yml     | 全局配置文件                                                 |         需要         |
| packages.json   | 记录了Hexo需要的包的信息，之后换电脑终端时npm根据这个信息来安装Hexo环境 |         需要         |

***

## 参考

* [Hexo在多台电脑上提交和更新-Kakaluotuo](https://blog.csdn.net/K1052176873/article/details/122879462) 

* [Git教程-廖雪峰](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)（补习git基本知识）
* [笔记-Git Github 简明教程](https://swibinchter.github.io/2017/01/11/Git-Github-%E7%AE%80%E6%98%8E%E6%95%99%E7%A8%8B/)

