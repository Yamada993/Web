---
layout: post
title:  "使用Windows Server做软路由"
subtitle: '软路由'
date:   2022-07-04
tags: 软路由
description: 'WIN软路由'
color: 'rgb(154,133,255)'
cover: '../assets/winsrv-j3160.png'
---

# Windows server 2022 配置软路由

![image-20220704170127825](D:\Web\yamada_web\yamada\assets\2022-7-4\image-20220704170127825.png)

系统采用 Microsoft Windows Server 2022来进行设置，**安装操作系统，配置远程访问等略过**

## 添加角色和功能

打开`服务器管理器`，在右上角点击`管理`，选中`添加角色和功能`

![image-20220704170818820](D:\Web\yamada_web\yamada\assets\2022-7-4\image-20220704170818820.png)

![image-20220704171152019](D:\Web\yamada_web\yamada\assets\2022-7-4\image-20220704171152019.png)

点击`下一步`，选择`基于角色或功能的安装`，点击`下一步`，选择`从服务器池中选择服务器`，选中当前需要设置的服务器后点击`下一步`

![image-20220704171738836](D:\Web\yamada_web\yamada\assets\2022-7-4\image-20220704171738836.png)

勾选DHCP服务器、DNS服务器、远程访问DirectAccess和VPN（RAS），点击下一步

![image-20220704174552334](D:\Web\yamada_web\yamada\assets\2022-7-4\image-20220704174552334.png)

在功能中选择`无线LAN服务`，远程服务器管理工具中选择`DHCP服务器工具`和`DNS服务器工具`，选择安装，安装完成后根据提示进行重启

![image-20220704175728254](D:\Web\yamada_web\yamada\assets\2022-7-4\image-20220704175728254.png)![image-20220704175823912](D:\Web\yamada_web\yamada\assets\2022-7-4\image-20220704175823912.png)

# 路由和远程访问

> 注意！如果你的无线网卡不是PCIE（内置），你是使用usb无线网卡作为ap，那么你应该首先启用承载网络，win server中无法直接打开承载网络，也就是win 用户系统中的热点共享，若你使用独立ap，通过RJ45网线进行使用WIFI功能，那么可以忽略此提示，并跳转到路由设置。

## WIFI设置

若你的无线网卡为USB，首先需要查看你的USB无线网卡是否支持承载网络和软ap，若支持，请在网卡的驱动设置里，启用ap模式

![image-20220704180838843](D:\Web\yamada_web\yamada\assets\2022-7-4\image-20220704180838843.png)

如何查看是否支持软ap和承载网络：

打开CMD窗口，输入 `netsh wlan show all`，在显示驱动程序中可以查看是否支持承载的网络，在显示接口功能一栏中可以看到此网卡是否支持软ap

![image-20220704181141549](D:\Web\yamada_web\yamada\assets\2022-7-4\image-20220704181141549.png)

![image-20220704181404065](D:\Web\yamada_web\yamada\assets\2022-7-4\image-20220704181404065.png)

## 开启承载网络

进入CMD窗口，输入`netsh wlan set hostednetwork mode=allow ssid=xxx key=xxx`其中，mode为指定允许还是禁止托管网络，ssid为你的无线网（WIFI）的名称，key为你的无线网密码

该命令成功执行后，此时输入WIN+R键，输入ncpa.cpl或者在控制面板中选择网络和Internet->网络连接，可以看到网络设备中多出一个本地连接*xx，名称为Microsoft Hosted Network Virtual Adapter...那么承载网络已成功添加，但是还是无法进行设备连接，此时，在CMD窗口中，输入`netsh wlan start hostednetwork`即可启用承载网络，当承载网络启用后，此WIFI才可被扫描到

## 路由设置

打开服务器管理器，点击左上角的工具，选择路由和远程访问，

![image-20220704180211997](D:\Web\yamada_web\yamada\assets\2022-7-4\image-20220704180211997.png)

> 若你在配置承载网络之前就启用了路由和远程访问服务，那么可能会出现网络接口中没有此虚拟网卡的情况，那么需要你先禁止路由和远程访问服务，先启用承载网络后再启用路由和远程访问服务



[https://docs.microsoft.com/zh-cn/troubleshoot/windows-server/networking/set-up-routing-remote-access-intranet]: 	"配置路由和远程访问相关连接"

