---
layout: post
title: "修复升级OS X后，VirtualBox的USB问题"
comments: true
---

**简而言之，重装VirtualBox就可以解决升级USB Devices找不到的问题。**

由于某些原因，我需要在虚拟机连接USB打印机进行打印。今天发现连接不到打印机，原因是虚拟机的USB Devices列表是空的。联想到昨天升级了OS X 10.11，猜测是因为升级系统导致的。

首先想到的是OS X EI Capitan与VirtualBox有兼容性问题。那么这对于当前来说应该无解的，除非VirtualBox推出支持EI Capitan的新版本，检查了一下VirtualBox已经是最新版了。这个时候想到的解决方案是：在OS X系统中共享打印机，然后在虚拟机中连接网络共享打印机。

本来我已经可以接受、并且满意上述方案了。但是想到银行的USB Key就不能使用，这个就没有其他替代方案了并且影响比较大。于是我想到每次OS X升级都会清空很多东西，比如/usr/bin这种系统目录下用户增加的文件之类。

所以另外一个可能性是：OS X升级之后把VirtualBox安装到某些系统目录的驱动文件给删除了，并且VirtualBox的USB功能依赖与这些文件。那么如果是这个情况的话，也很好测试，重新安装VirtualBox。安全起见我还重装了VirtualBox Extension Pack。然后It Works！所以我得到了本文最上面的解决方案。

由于我不但重装了VirtualBox，还重装了VirtualBox Extension Pack，导致目前不确定重装某一个，还是需要两者都重装。如果你也遇到这个问题，可以顺便探索一下。
