---
title: Android Studio踩坑日记
date: 2022-07-04 23:56
tags:
- 移动开发
- Idea配置
categories:
- 移动开发
---

Created: March 5, 2022 1:20 AM

- 打开Android Studio就要设置代理？
    
    ![代理设置界面](http://pic.xishng.top/img/202207050028169.png)

    在使用代理上，可以参考下列信息：
    
    ```bash
    // 东软信息学院
    mirrors.neusoft.edu.cn:80
    
    // 北京化工大学
    ubuntu.buct.edu.cn/ubuntu.buct.cn:80
    
    // 中国科学院开源协会
    mirrors.opencas.cn:80
    mirrors.opencas.org/mirrors.opencas.ac.cn:80
    
    // 上海GDG镜像服务器
    sdk.gdgshanghai.com:8000
    
    // 电子科技大学
    mirrors.dormforce.net:80
    ```
    
    如果使用上面的URL链接，那么到这里就完成了。
    
- 协议接受后，需要的工作
    - SDK Tools
        
        ![SDK Tools界面](http://pic.xishng.top/img/202207050029803.png)
        
        我所用的是MAC，所以这里所圈中的是必不可少的SDK工具。
        
    - 安卓镜像
        
        ![安卓镜像列表](http://pic.xishng.top/img/202207050029525.png)
        
        图中圈画出来的需要注意下
        
        - Google Play：不支持ROOT
        - Google APIs：支持ROOT
            - 通过 ~/Library/Android/sdk/platform-tools/adb root 即可开启（路径不一致请自行修改，一般来讲都在上面下单SDK Tools文件目录中）
- 模拟器联网问题
    - 简单的解决方法
        
        关闭代理，然后重新创建模拟器，然后在使用的时候关闭模拟器的WIFI，仅保留移动通信功能，此时就可以正常使用了。
        
    - 略微复杂，这里会讲述两种方法：
        1. 「临时解决方案」首先关掉你的模拟器，同样在SDK Tools的下载目录下，通过如下命令打开你的模拟器即可解决：
            
            ```bash
            $ ~/Library/Android/sdk/emulator/emulator --avd <你模拟器的名字> -dns-server 8.8.8.8,114.114.114.114
            ```
            
        2. 【看看就行了，没用】「网传永久有效方案」打开你的模拟器（一定要是Google APIs类型的哦），执行下面命令：
            
            ```bash
            # 打开root权限
            $ ~/Library/Android/sdk/platform-tools/adb root
            restarting adbd as root
            
            $ ~/Library/Android/sdk/platform-tools/adb shell
            
            emulator64_arm64:/ # getprop
            ...
            [net.dns1]: [fec0::3]
            [net.dns2]: [10.0.2.3]
            ...
            emulator64_arm64:/ # setprop net.dns1 8.8.8.8
            
            emulator64_arm64:/ # getprop
            ...
            [net.dns1]: [8.8.8.8]
            [net.dns2]: [10.0.2.3]
            ...
            ```
            
            按照网上的教程，此时重启模拟器就可以正常使用了。