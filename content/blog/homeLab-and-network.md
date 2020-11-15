+++
title = "HomeLab and Network 家庭实验室和网络"
date = "2020-11-14T18:15:23+08:00"
tags = ["HomeLab", "Network"]
comments = true
+++

突然想起某样东西，然后又突然把它忘了的情况，最近发生了很多次。部署的设备和软件已经不少，在这里记录一下，供自己翻阅。

![](/images/20201114195243.png)

群晖安装的硬盘是希捷监控盘 4T x 2 SHR RAID1，防止断电使用的是 APC UPS BK650M2-CH，DDNS 和 OpenVPN Server 都用的群晖官方的，使用和维护很方便，有公网 IP，DDNS 搭配 UPnP 或者 QuickConnect 都很方便。

在 ESXi上跑了 Ubuntu，学习 Kubernetes。通过 UPnP 把 API 映射在外网，相当于有了一台属于自己的 “云服务器”。

主路由器用的 EdgeRouter X，把之前的几个无线路由当作 AP，家里有很多小米的设备，无线一直不太稳定，这是一个问题得想办法解决。

代理通过自建出口，防止 IP 频繁变动，通过中转保护和优化线路，防止记录信息。

EdgeRouter X
* DNS -> Adguard Home
* UPnP

群晖 DS718+
 *  DDNS
 *  OpenVPN Server
 *  Active Backup for Business -> 备份 ESXi
 *  Docker
    * Adguard Home -> DoH
    * Aria2
    * Clash
    * MySQL
    * Docker Registry

ESXi J3455-ITX
* Ubuntu -> k3s KubeSphere

Mac
* Tunnelblick
* ClashX
* Synergy -> 多台电脑鼠标同步
* Multipass -> 几分钟之内即可启动并运行虚拟机
    
IOS
* vSphere Mobile Client
* Potatso Lite
* Shadowrocket
* OpenVPN Connect

代理
* Shadowsocks And v2ray-plugin -> 使用 Docker 部署
* VPS
    * BandwagonHost LA 1C512M
    * 阿里云轻量 HK 1C1G
* 流量转发
    * WikiHost
    * Cloudflare
