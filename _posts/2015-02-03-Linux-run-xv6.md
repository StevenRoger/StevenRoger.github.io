---
title: 在Linux x86_64环境运行xv6系统
description: xv6是Unix第六版本，针对x86架构的一个现代实现，我们就试着在Linux下跑起来
categories:
- Linux
tags:
- Linux
---


xv6是MIT针对`6.828: Operating System Engineering`课程而开发的简单的、类Unix的实验性操作系统。xv6是Unix第六版本，针对x86架构的一个现代实现。同时，在国内外还有很多高校也在采用。

xv6系统。目前在MIT的开放课程中依然可以找到2013年之前的内容，如针对2014年的秋季学期的课程。其主讲还是Russ Cox，Go语言的主要开发者，Plan9等系统的作者，一位非常知名的操作系统专家。这是不可多得的一个开放课程，其中还提供了非常丰富的学习资料，建议有能力的同学都去学一下。xv6提供了针对bochs和qemu虚拟环境的编译、调试和运行支持。我曾经在Ubuntu 32位Linux环境研究过，觉得非常好。在学习lua的时候，我还将其中的代码排版系统也移植过去(请看我的开源项目guiquanz/Lua-5.2.1)。后来，迁移到Fedora x86_64环境之后，就没在捣鼓了。今天，在看《Unix内核源码剖析》一书，温故操作系统知识时，突然又想起了xv6，所以又回来扯蛋了。以下内容针对Fedora x86_64qemu环境（非Fedora的x86_64系统，相关操作也类似），如果你的是32位的x86系统，请参考[Homework: running and debugging xv6](http://zoo.cs.yale.edu/classes/cs422/2014/lec/l2-hw)即可。

让vx6飞起来
安装x86 32位头文件
> sudo yum install -y glibc-devel.i686

安装qemu
> sudo yum install -y qemu

克隆xv6代码（需要git工具）
```
mkdir -p ~/xv6 && cd ~/xv6
git clone git://pdos.csail.mit.edu/xv6/xv6.git
```
编译xv6

> cd xv6
> make

运行xv6
> qemu-system-i386 -serial mon:stdio -hdb fs.img xv6.img -smp 1 -m 512

或

> qemu-system-x86_64 -smp 1 -parallel stdio -hdb fs.img xv6.img -m 512