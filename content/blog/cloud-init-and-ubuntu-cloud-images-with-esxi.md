+++
title = "ESXi 部署 Ubuntu Cloud Image"
date = "2021-01-02T13:55:45+08:00"
tags = ["HomeLab","ESXi"]
comments = true
+++

需要一个干净的基础镜像，并且在几分钟之内即可启动并运行虚拟机，[Multipass](https://multipass.run) Mac 或 Windows 工作站上的迷你云，作为开发使用特别方便，但部署一个长期运行的服务器时，使用起来不太适合。

[Ubuntu Cloud Images](https://cloud-images.ubuntu.com) 是官方的Ubuntu映像，并且在镜像中集成了 [Cloud Init](https://cloudinit.readthedocs.io)，Cloud Init 是用于跨平台云实例初始化的行业标准，作为基础镜像最合适不过，实现磁盘开机时的自动扩容，只需配置：

```
# cloud-config
rowpart:
  mode: auto
  devices: ['/']
```

Ubuntu Cloud Images 下载镜像中提供的 [开放虚拟机格式文件（Open Virtualization Format，OVF）](https://zh.wikipedia.org/zh-hans/%E5%BC%80%E6%94%BE%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%A0%BC%E5%BC%8F%E6%96%87%E4%BB%B6)，可以直接部署到 ESXi，并且导入时能直接填写密码或其他配置，开箱即用。

下载镜像后，登陆 ESXi Web 客户端，创建/注册虚拟机，选择 从 OVF 或 OVA 文件部署虚拟机，在页面中填入密码，等待导入完成，即可启动虚拟机。

![](https://i.v2ex.co/WP4uVx9H.png)

但是，进入系统后登陆却提示密码错误，一番查阅资料后，在一篇博客 [How to finally inject OVF properties into VCSA when deploying directly onto ESXi?](https://www.virtuallyghetto.com/2014/05/how-to-finally-inject-ovf-properties-into-vcsa-when-deploying-directly-onto-esxi.html) 找到原因。

> OVF 属性的工作方式是：通过 VMware Tools 注入虚拟设备，并在打开虚拟设备电源时将其注入 OVF 运行时环境。如果用户决定在部署后不立即打开虚拟设备电源，则必须保留 OVF 属性及其值。对于 vCenter Server，我们有一个数据库，可以在其中存储 OVF 属性，但是对于 ESXi，不存在可以正确存储 OVF 属性的数据库，这就是 ESXi 不支持它们的原因。

文章中有多种方式解决这个问题，因为只部署了单台 ESXi，所以此处选择手动注入的方式，参考 [An alternate way to inject OVF properties when deploying virtual appliances directly onto ESXi](https://www.virtuallyghetto.com/2014/06/an-alternate-way-to-inject-ovf-properties-when-deploying-virtual-appliances-directly-onto-esxi.html) ，这是在直接部署到 ESXi 时使 OVF 属性起作用的巧妙方法，使用手动注入的方式，对于需要密码的虚拟机，还需要在 guestinfo.ovfEnv 中公开纯文本密码，不建议使用该方法。

![](https://i.v2ex.co/mPq8az2S.png)

到此，虚拟机启动完成，在控制台中，可以通过命令 `vmtoolsd --cmd 'info-get guestinfo.ovfEnv'` 检查注入的 OVF 属性。

![](https://i.v2ex.co/yxO92vVl.png)