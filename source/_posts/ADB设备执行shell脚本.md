---
title: ADB设备执行shell脚本
tags: 
- Shell
- Adb
categories:
- 技术
date: 2022-11-07 10:57:11
---

# ADB设备执行shell脚本

------

## 前记

1. 需求是在多台安卓设备上进行持续性点击的压力测试，直到出现异常，无需对结果进行自动断言，设备已设置无线ADB连接，个人希望设备能脱离电脑自动执行
2. 最后采用的方法是，编写shell循环执行脚本-连接adb设备-推到adb设备上-建立线程自动执行
3. 将上述ADB连接-推送-执行的步骤，编写为自动执行的脚本

<!--more-->

------

## 推送和执行的方法

### 半自动推送和执行

- 编写了一个`Autorun.sh`，直接执行即可，代码如下：

  ```shell
  #!/bin/bash
  
  echo '--------------------------------------------------------------------------------'
  echo "欢迎光临........."
  echo "使用前请务必确认要执行的脚本文件无误！！！"
  echo '--------------------------------------------------------------------------------'
  echo "正在重启adb服务........."
  
  adb kill-server
  
  adb start-server
  
  while(true)
  do
      echo "正在重置adb设备的连接........."
  
      adb disconnect
  
      echo "请将要执行的sh脚本命名为test.sh，确保所在路径无中文后，拖到此窗口，使用<Enter>键确认："
      read  code_dir
      echo '--------------------------------------------------------------------------------'
      echo "请确保设备与本电脑在同一WIFI下，进入工程模式打开设备的ADB_WIFI"
      read -p '请输入设备上对应的IP地址(如192.168.100.170)：'  ip
      read -p '请输入设备上对应的PORT端口(如9981)：'  port	
      echo "正在连接设备  $ip:$port ........."
  
      adb connect $ip:$port
  
      echo "成功连接的设备如下："
  
      adb devices
  
      echo '如未能成功连接adb设备，请使用<Ctrl + C>退出'
      echo '--------------------------------------------------------------------------------'
      echo '成功连接后，请将设备退出工程模式，等待设备返回应用主界面(根据脚本起始界面定)'
      read -p '等待设备返回应用主界面(根据脚本起始界面定)后，使用<Enter>键继续：'
  
      adb root
  
      adb root
  
      # GitBash在执行ADB命令时需要在目录前多加一个/，不然会报错
      adb push $code_dir //data/
  
      adb shell cd //data/
  
      adb shell chmod 777 //data/test.sh
  
      adb shell nohup //data/test.sh &
  
      echo "脚本开始执行，请在设备上验证........."
      echo "如需停止脚本执行，请直接重启设备或断开电源........."
      echo '--------------------------------------------------------------------------------'
      read -p "使用<Enter>键添加下一台设备，使用<Ctrl + C>键退出："
  done
  ```



### 手动推送和执行

1. 确保设备的ADB开关为开
2. adb disconnect
3. 连接adb
4. adb root
5. adb push 路径/脚本.sh /data/
6. adb shell
7. su root
8. cd /data/
9. chmod 777 脚本.sh
10. 确保设备在起始页面(根据脚本的开始界面而定)
11. nohup ./脚本.sh &  # 会返回进程id
12. 设备上验证，如需停止执行将设备重启即可
13. 后续管理
    1. jobs -l # 查看运行的后台进程，可以看到进程id
    2. kill -s 9 进程id # 杀掉进程，停止执行
    3. rm 脚本.sh  # 删掉脚本
    4. exit  # 退出shell，需要重复执行多次才能完全退出



------

## ADB设备上执行的脚本示例

- 循环点击后的窗口上输入数字-删除数字-关闭窗口

  ```shell
  #!/system/bin/sh
  i=1
  while ((i=1))
  do
  	# 点击位置使用x,y像素坐标确定，可以通过Appium桌面版连接后查看
  	# 点击输入证件号
  	input tap 200 1230
  	# 休眠1秒
  	sleep 1
  	# 输入数字147X258X369147X258
  	input tap 180 720
  	# sleep 1
  	input tap 180 830
  	# sleep 1
  	input tap 180 940
  	# sleep 1
  	input tap 180 1050
  	# sleep 1
  	input tap 400 720
  	# sleep 1
  	input tap 400 830
  	# sleep 1
  	input tap 400 940
  	# sleep 1
  	input tap 180 1050
  	# sleep 1
  	input tap 620 720
  	# sleep 1
  	input tap 620 830
  	# sleep 1
  	input tap 620 940
  	# sleep 1
  	input tap 180 720
  	# sleep 1
  	input tap 180 830
  	# sleep 1
  	input tap 180 940
  	# sleep 1
  	input tap 180 1050
  	# sleep 1
  	input tap 400 720
  	# sleep 1
  	input tap 400 830
  	# sleep 1
  	input tap 400 940
  	sleep 1
  	# 删除部分数字
  	input tap 685 475
  	# sleep 1
  	input tap 685 475
  	# sleep 1
  	input tap 685 475
  	# sleep 1
  	input tap 685 475
  	# sleep 1
  	input tap 685 475
  	# sleep 1
  	input tap 685 475
  	# sleep 1
  	input tap 685 475
  	# sleep 1
  	input tap 685 475
  	# sleep 1
  	input tap 685 475
  	# sleep 1
  	input tap 685 475
  	# sleep 1
  	input tap 685 475
  	sleep 1
  	# 输入520
  	input tap 620 830
  	# sleep 1
  	input tap 620 830
  	# sleep 1
  	input tap 620 830
  	# sleep 1
  	input tap 400 830
  	# sleep 1
  	input tap 400 720
  	# sleep 1
  	input tap 400 1050
  	sleep 1
  	# 点击确定
  	input tap 620 1050
  	sleep 1
  	# 点击关闭
  	input tap 715 245
  	sleep 1
  done
  ```



------

