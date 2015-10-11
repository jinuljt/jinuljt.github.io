---
layout: post
title: "修复升级OS X后，VirtualBox的USB问题"
comments: true
---

**简而言之，重装VirtualBox就可以解决升级USB Devices找不到的问题。**

由于某些原因，我需要在虚拟机里面打印。一直以来都是在虚拟机中通过USB连接到打印机来进行打印的。今天突然发现虚拟机的USB Devices列表是空的。联想到昨天升级了OS X系统到10.11，我估计是因为升级系统导致的。

首先想到的是OS X EI Capitan与VirtualBox的兼容性问题。那么这个问题对于当前来说就是无解的，除非VirtualBox升级新版本，检查了一下，已经是最新版了。这个时候如果我想要打印的话，解决方案是，OS X系统中共享打印机，然后在虚拟机中连接网络共享打印机。

本来我已经很满意第一个方案了，但是想到银行的USB Key就不能使用了，这个影响比较大。于是我想到每次OS X升级都会清空很多东西，比如/usr/bin下面我自己增加的文件之类。

那么另外一个可能性是：OS X升级之后把VirtualBox安装的驱动文件给删除了，VirtualBox的USB功能依赖与这些驱动文件。这个问题很好测试，重新安装VirtualBox。安全起见我还重装了VirtualBox Extension Pack。然后It Works！所以我得到了本文最上面的解决方案。

由于我不但重装了VirtualBox，还重装了VirtualBox Extension Pack，导致目前不确定重装某一个，还是需要两者都重装。如果你也遇到这个问题，可以顺便探索一下。
