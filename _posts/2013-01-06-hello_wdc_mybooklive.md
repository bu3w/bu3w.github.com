---
layout: post
title: My Book Live 的改造
---

## 产品

这段时间重新规划了一下居住地的局域网。路由更换为千兆路由[NETGEAR WNDR3700](http://www.netgear.com/home/products/wirelessrouters/high-performance/wndr3700.aspx)([我的购买地](http://www.amazon.cn/gp/product/B008M422RA))，并购入简单的网络存储设备 [WDC My Book Live](http://wdc.com/en/products/products.aspx?id=280)([我的购买地](http://www.amazon.cn/gp/product/B004RZVCRE))这样局域网内的机器可以直接访问网络硬盘上的数据。不用每个机器都保持一份数据，不用保持各个机器之间的同步。并且以后购买的PC的硬盘可以不用很大，只存储必要数据即可。

当然我曾考察过群晖的产品，例如：[DS713+](http://www.synology.com/products/product.php?product_name=DS713%2B&lang=enu)，[DS213+](http://www.synology.com/products/product.php?product_name=DS213%2B&lang=enu)，[DS112+](http://www.synology.com/products/product.php?product_name=DS112%2B&lang=enu)等。看评测性能不错，但是价格偏高。在实际使用过程中，感觉这款 WDC 的硬盘的性能在笔记本的2.5寸硬盘之上，大概在30~40M/s(使用中的数据，非评测数据)。

## 软件

My Book Live 的处理器是 600MHz 的 PowerPC, 256MB内存，3.5寸硬盘。系统是 Debian，附加 WDC 的软件(代码)。Debian 应该是 lenny 版本的。这个系统安装在硬盘上自己的分区内。自身的远程存取服务是通过 OpenVPN 实现的。VPN 回 WDC 的服务器，在通过 java applet 展示在我们的浏览器里。

## 改造

请参考 http://mybookworld.wikidot.com/mybook-live

http://mybookworld.wikidot.com/start 是一个好站，有用的信息很多。

* 启用 SSH
    浏览器访问硬盘的 http://WD_MBL_IP/UI/ssh 按界面操作可启用 ssh 登陆
    需要在局域网内其他机器
* 配置 debian 源
    nano /etc/apt/sources.list
    我启用了 squeeze 的源
* 安装 Debian keyring
    aptitude install debian-keyring
    安装 keyring 之后可以安装 debian 源的其他软件
* 在路由器或网络硬盘上，用自己的方法启用 DDNS 。据说 NETGEAR 上使用花生壳是需要收费版的才可以。
* 修改 /etc/contentdir
    WDC MBL 上使用 forked-daapd 来提供 iTunes 的共享服务。但是，默认的配置，它会扫瞄整个 Public 文件夹，象我这样把多数文件都放在 Public 下，会导致 forked-daapd 扫瞄过多的文件，而且它会耗尽所有内存，根本完不成扫瞄。一句话，会变砖。限制 forked-daapd 只扫瞄 Music 和 Videos 文件夹。内容如下：
> +V|/Public/Videos,+M|/Public/Music

另外，我还安装了 tmux，git。这样，我在外的时候，可以通过 SSH 连回这块硬盘进行简单的管理。可以进行 HTTP/FTP 的下载，更重要的，可以将硬盘当做 git 的镜像服务器，做一次缓存/备份。

最开始，我曾尝试过在这块硬盘上安装 PPTP VPN可以，但是需要需要使用自己编译内核，因为默认的内核里没有 iptables 依赖的模块。编译/使用自定义内核是可以的，并且成功过。在 wikidot 上有文档。也试用过VPN。但最后还是放弃了。因为试用ssh也可以简单的管理，VPN在有些网络上是禁用VPN的，但是最重要的原因是 联通的破网络上传带宽只有50KB/s，这也好意思说是宽带。(我挺希望VPN服务的。安全，而且方便)

\-\-\-\-\- 2013-MAR-03 UPDATE \-\-\-\-\-

在默认情况下，/shares/Public 对应的分区不可以修改文件，只能创建文件时一次写入文件内容。如果想去掉这个限制，那么需要下面这个命令。如需进一步消息请搜索这个命令。

    sudo /usr/local/bin/settrustees -D

\-\-\-\-\- 2013-MAR-24 UPDATE \-\-\-\-\-

在MBL的系统中，如果aptitude update可能会有类似的提示

```
W: GPG error: http://ftp.us.debian.org squeeze Release: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY AED4B06F473041FA
W: GPG error: http://ftp.us.debian.org wheezy Release: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY AED4B06F473041FA
W: You may want to run apt-get update to correct these problems
```

可以使用如下命令，添加证书

```
sudo apt-key adv --keyserver pgpkeys.mit.edu --recv-keys AED4B06F473041FA
```

\-\-\-\-\- 2013-08-15 UPDATE \-\-\-\-\-
经历了变砖之后，我弃用原来这个办法了。转向 optware 方案。同样每次升级之后都需要重新安装自己手动安装的这些软件。优点是，不必像 apt 方案那样，每次升级之后安装策略都有可能不一样…
optware 的安装方法详见：http://mybookworld.wikidot.com/optware
