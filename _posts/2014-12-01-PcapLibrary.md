---
title: Pcap库的安装及网络环境的搭建
description: Linux环境下安装libpcap库，以及简单使用该库
categories:
- Linux
tags:
- Linux
---

总是在决定写这些东西的时候觉得时间不够，脑子刚闪现出这些矛盾，同时就被自我否定打败：没有时间就说明自己做事情的效率不高，也说明了自己的时间安排得不够合理。

废话不多说，现在就开始和大家一起在Linux环境下安装libpcap库吧！

### 准备工作：下载相关文件

由于部分相关文件的下载网站在校园网内无法访问（我就遇到这情况），我就找了代理，下载了所有安装包，直接放在了我的网盘上，[网盘链接](http://pan.baidu.com/s/1jGoVeGQ)，方便大家下载(*_*)

常规情况下的安装，需要4个安装包（随着Linux发行版的发展，很多宏处理、解析器都在系统中自带），我们可以先检查自己的系统有没有安装m4、bison以及flex，它们可以说是libpcap库的安装依赖吧，分别用以下几个简单的命令：

```sh
m4 --version
m4 (GNU M4) 1.4.13 

bison --version
bison (GNU Bison) 2.4.1

flex --version
flex 2.5.35
```

任何一个没有安装的话，就按以下步骤安装吧！在Linux下以.gz结尾的压缩包就都用命令`tar -zxvf 压缩文件名`来解压。我还是和大家一起了解一下这几个安装包的作用吧。

* flex，官方说明是`A fast, Open Source and cross-platform lexical analyzer and scanner generator tool`。如果没有编译安装flex文件，在编译安装libpcap时，就会出现 `configure: error: Your operating system's lex is insufficient to compile libpcap.`的错误提示。 

* GNU Bison实际上是使用最广泛的Yacc-like分析器生成器,使用它可以生成解释器,编译器,协议实现等多种程序. 它不但与Yacc兼容还具有许多Yacc不具备的特性。如果没有编译安装bison文件，在编译安装libpcap时，就会出现 `configure: WARNING: don't have both flex and bison; reverting to lex/yacc checking for capable lex... insufficient` 的错误提示。

* M4 是一个宏处理器.将输入拷贝到输出,同时将宏展开. 宏可以是内嵌的也可以是用户定义的. 除了可以展开宏,m4还有一些内建的函数,用来引用文件,执行Unix命令,整数运算,文本操作,循环等. m4既可以作为编译器的前端也可以单独作为一个宏处理器。如果没有编译安装m4文件，在编译安装bison时，就会出现 `configure: error: GNU M4 1.4 is required`的错误提示。

### 开始安装：

依次进入目录m4-1.4.9，bison-3.0，flex-2.5.39，libpcap-1.2.0 并执行以下命令：
```sh
./configure

make

make install
```

这会儿要注意一个问题了，我们一般都是在虚拟机上跑Linux，而且这些安装包默认就放在Linux和windows的共享文件夹下，而且大多数的安装教程都是直接执行以上命令。我就深受其害，连./configure都未能正常生成Makefile。首先高版本的libpcap对安装环境有更严格的要求，而我们的共享文件夹则是Linux中的另一个分区，用"ln"命令来创建符号链接的时候，它产生的是一个硬链接，硬链接不能跨分区，导致各种出错。我么就将这些安装包全部复制到Linux的/home目录下解压，然后依次执行以上命令，是没有问题的！

### 安装测试：
这里有一个简单的测试文件sniff.c

```c
#include <stdio.h>
#include <pcap.h>

int main(int argc,char *argv[])
{
  char *dev,  errbuf[PCAP_ERRBUF_SIZE];
  dev = pcap_lookupdev(errbuf);

  if ( dev == NULL)
  {
    fprintf(stderr,"couldn't find default device: %s\n", errbuf);
    return (2);
  }  

  printf ("Device: %s\n", dev);
  return (0);
}

```

编译：
> gcc -o sniff sniff.c -lpcap

执行：
> ./sniff

这样就可以打印出当前正在运行的网卡设备名了！