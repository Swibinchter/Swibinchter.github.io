---
title: Python调用shell命令(含adb)
date: 2022-11-03 17:36:35
tags: [Python]
categories: 技术
---

# Python调用shell命令(含adb)

***

## 调用方式

1. [subprocess模块调用](#subprocess)【推荐，它出现就是为了替代os模块、commands模块等 】，一般使用`run()`，若无需等待命令执行完毕就往下进行的话使用`Popen()`
2. [os模块调用](#os)【不推荐】
3. [commands模块调用](#commands)



------

## 常见问题

### 命令格式的问题

1. 命令有特殊字符，如切换目录时中有`/`，需要使用`r'命令字符串'`防止转义
2. 命令的目录中如果有`Program Files (x86)`时，对部分cmd版本识别有问题无法正常执行，将目录使用引号包起来，如`cd 'C:\Program Files (x86)\Appium'`

### 常用的Shell命令

```shell
# 安装Appium的win.exe文件后，启动Appium服务，需要在main.js所在的目录下运行，一般是C:\Program Files (x86)\Appium\resources\app\node_modules\appium\build\lib
node main.js

# 查看端口情况，筛选包含有":4437"字符的，如存在返回的结果中包含进程pid
netstat -nao | findstr :4723

# 中止进程，可以用于中止Appium服务，进程pid可以从上个命令中获取
taskkill /pid 进程pid -t -f
```

###  常用的Adb命令

```shell
# 查看当前连接设备
adb devices

# 无线连接设备，通过ip和端口
adb connect IP地址:端口

# 断开所有连接
adb disconnect
```



***

##一、<a id="subprocess">subprocess模块调用</a>【常用方法】

* 允许创建很多子进程，创建的时候能指定子进程和子进程的输入、输出、错误输出管道，执行后能获取输出结果和执行状态。

* 运行python的时候，我们都是在创建并运行一个进程。像Linux进程那样，一个进程可以fork一个子进程，并让这个子进程exec另外一个程序。在Python中，通过标准库中的subprocess包来fork一个子进程，并运行一个外部的程序。 

* Python自带的模块，Python版本建议大于3.5，使用时导入

  ```python
  import subprocess
  ```

### subprocess.run()【推荐使用】

* **常用示例**

    ```python
    subprocess.run("adb devices",capture_output=True,shell=True,cwd=r'C:\Users',encoding='utf-8')   # 执行shell命令，获取连接的adb设备
    subprocess.run(['adb','devices'],capture_output=True,shell=True,cwd=r'C:\Users',encoding='utf-8').stdout # 执行shell命令，获取连接的adb设备，并获取输出结果
    ```

* **格式**

    ```python
    subprocess.run(args,*, stdin=None, input=None, stdout=None, stderr=None, capture_output=False, shell=False, cwd=None, timeout=None, check=False, encoding=None, errors=None, text=None, env=None, universal_newlines=None)
    ```

* **主要参数**

    * `args`：要执行的shell命令,字符串/字符串列表，列表的话会使用空格将其串起来，字符串的话需要`shell=True`
    * `stdin、stdout、stderr`：子进程的标准输入、输出和错误。其值可以是 subprocess.PIPE、subprocess.DEVNULL、一个已经存在的文件描述符、已经打开的文件对象或者 None。subprocess.PIPE 表示为子进程创建新的管道。subprocess.DEVNULL 表示使用 os.devnull。默认使用的是 None，表示什么都不做。另外，stderr 可以合并到 stdout 里一起输出。
    * `timeout`：设置命令超时时间。如果命令执行时间超时，子进程将被杀死，并弹出 TimeoutExpired 异常。
    * `check`：如果该参数设置为 True，并且进程退出状态码不是 0，则弹出 CalledProcessError 异常。
    * `encoding`: 如果指定了该参数，则 stdin、stdout 和 stderr 可以接收字符串数据，并以该编码方式编码。否则只接收 bytes 类型的数据。
    * `shell`：如果该参数为 True，将通过操作系统的 shell 执行指定的命令。
    * `cwd`：在哪个目录下执行命令
    * `capture_output`： 如果`capture_output=True`，则将捕获stdout和stderr，调用时内部的Popen对象将自动使用`stdout=PIPE`和`stderr = PIPE`创建标准输出和标准错误对象；传递`stdout`和`stderr`参数时不能同时传递`capture_output`参数。如果希望捕获并将两个stream合并为一个，使用`stdout=PIPE`和`stderr = STDOUT`。 

* **返回值**：`CompletedProcess`类，包含下列内容

    * `args`：启动进程的参数,通常是个列表或字符串

    * `returncode`：进程结束状态返回码,0表示成功状态

    * `stdout`：命令执行后的输出信息，通常为bytes类型序列，需要提前设置`encoding`，None表示没有捕获值

    * `stderr`：命令执行后的错误信息，通常为bytes类型序列，需要提前设置`encoding`，None表示没有捕获值

    * `check_returncode() `：用于检查返回码。如果返回状态码不为零，弹出CalledProcessError异常。

        ```python
        CompletedProcess(args=['adb', 'devices'], returncode=0, stdout=b'List of devices attached\r\n\r\n', stderr=b'')
        ```


### subprocess.Popen()

* subprocess模块中最基础的构造函数，上面讲的run()就是基于它的简化版

- **常用示例**

  ```python
  p = subprocess.Popen("adb devices", shell=True, encoding="utf-8", stdout=subprocess.PIPE)  # 执行shell命令，获取连接的adb设备
  p.stdout   # 获取输出结果
  p.kill()	# 杀死进程
  p.wait()	# 等待进程中止后再继续
  
  subprocess.Popen('node main.js',shell=True,cwd=r'C:\Program Files (x86)\Appium\resources\app\node_modules\appium\build\lib') # 启动Appium服务
  subprocess.Popen("netstat -nao | findstr :4723", shell=True, encoding="utf-8", stdout=subprocess.PIPE).stdout  # 根据对应的端口判断Appium服务是否启动
  
  ```

- **格式**

  ```python
  subprocess.Popen(args, bufsize=-1, executable=None, stdin=None, stdout=None, stderr=None, preexec_fn=None, close_fds=True, shell=False, cwd=None, env=None, universal_newlines=False, startupinfo=None, creationflags=0, restore_signals=True, start_new_session=False, pass_fds=(), *, encoding=None, errors=None, text=None)
  ```

- **主要参数**

  - `args`：要执行的shell命令,字符串/字符串列表，列表的话会使用空格将其串起来，字符串的话需要`shell=True`
  - `bufsize`：缓冲区大小。当创建标准流的管道对象时使用，默认-1。
    * 0：不使用缓冲区
    * 1：表示行缓冲，仅当universal_newlines=True时可用，也就是文本模式
    * 正数：表示缓冲区大小
    * 负数：表示使用系统默认的缓冲区大小
  - `stdin、stdout、stderr`：子进程的标准输入、输出和错误。其值可以是 subprocess.PIPE、subprocess.DEVNULL、一个已经存在的文件描述符、已经打开的文件对象或者 None。subprocess.PIPE 表示为子进程创建新的管道。subprocess.DEVNULL 表示使用 os.devnull。默认使用的是 None，表示什么都不做。另外，stderr 可以合并到 stdout 里一起输出。
  - `preexec_fn`：只在 Unix 平台下有效，用于指定一个可执行对象（callable object），它将在子进程运行之前被调用。
  - `timeout`：设置命令超时时间。如果命令执行时间超时，子进程将被杀死，并弹出 TimeoutExpired 异常。
  - `check`：如果该参数设置为 True，并且进程退出状态码不是 0，则弹出 CalledProcessError 异常。
  - `encoding`: 如果指定了该参数，则 stdin、stdout 和 stderr 可以接收字符串数据，并以该编码方式编码。否则只接收 bytes 类型的数据。
  - `shell`：如果该参数为 True，将通过操作系统的 shell 执行指定的命令。
  - `cwd`：在哪个目录下执行命令

- **返回值**：Popen实例化的对象，包含下列方法

  - `poll()`：检查进程是否终止，如果终止返回 returncode，否则返回 None。
  - `wait(timeout)`：等待子进程终止。
  - **`communicate(input,timeout)`**：和子进程交互，发送和读取数据，当参数为空时，默认读取子进程输出结果，类型为元组(“输出结果”，“错误信息”)。
  - `send_signal(singnal)`：发送信号到子进程 。
  - `terminate()`：停止子进程,也就是发送SIGTERM信号到子进程。
  - `kill()`：杀死子进程。发送 SIGKILL 信号到子进程，kill之后建议跟上wait确定杀死
  - `pid`：获取子进程的进程ID。
  - `returncode`：获取进程的返回值。如果进程还没有结束，返回None。

  - **`stdout`**：命令执行后的输出信息，通常为bytes类型序列，需要提前设置`encoding`，None表示没有捕获值
  - `stderr`：命令执行后的错误信息，通常为bytes类型序列，需要提前设置`encoding`，None表示没有捕获值

### run()和Popen()的重要区别

| 区别       | run()                           | Popen()                                     |
| :--------- | ------------------------------- | ------------------------------------------- |
| 创建子进程 | 新创建一个进程，直接运行        | 新创建一个管道，放到后台运行                |
| 等待子进程 | 等待子进程执行完毕再继续        | 无需等待子进程执行完毕，立即往下进行        |
| 其他       | 相当于执行Popen()+communicate() | 可以调用communicate()向子进程传递和接受数据 |



***

## 二、<a id="os">os模块调用</a>

* Python自带的模块，使用前直接导入，主要处理操作系统相关的内容如对文件/文件夹的操作

    ```python
    import os
    ```
### os.system()

* **常用示例**

  ```python
  os.system("chdir") # 执行获取当前目录的shell命令
  ```

* **格式**

    ```python
    os.system(命令字符串)
    ```

* **参数**
  * shell命令，字符串形式
* **返回值**：命令的退出状态码
  * 执行成功返回0
  * 执行失败返回1或其他



### os.popen()

- **常用示例**

  ```python
  os.popen("chdir").read() # 执行获取当前目录的shell命令，返回结果
  ```

- **格式**

  ```python
  os.system(命令字符串)
  ```

- **参数**

  - shell命令，字符串形式

- **返回值**：命令的退出状态码

  - 执行成功返回0
  - 执行失败返回1或其他

- **方法**

  - read()可以将返回值转为字符串



### 注意事项

上述两个命令执行时默认会等待命令执行完毕才会继续进行，对于一些持续性命令，如开启Appium服务等并不友好，需要配合多线程并行使用



------

## 三、<a id="commands">commands模块调用</a>

- 非Python官方包，需要先安装，使用前直接导入，主要处理操作系统相关的内容如对文件/文件夹的操作

  ```python
  # 安装
  pip install command
  # 使用时导入
  import os
  ```

### commands.getstatusoutput()

- **常用示例**

  ```python
  commands.getstatusoutput("chdir") # 执行获取当前目录的shell命令
  ```

- **格式**

  ```python
  commands.getstatusoutput(命令字符串)
  ```

- **参数**

  - shell命令，字符串形式

- **返回值**：(status,output)

  - status：状态码
  - output：输出结果



### commands.getoutput()

- **常用示例**

  ```python
  commands.getoutput("chdir") # 执行获取当前目录的shell命令
  ```

- **格式**

  ```python
  commands.getoutput(命令字符串)
  ```

- **参数**

  - shell命令，字符串形式

- **返回值**：

  - output：输出结果



***