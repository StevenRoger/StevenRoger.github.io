---
title: Linux个人发展方向的选择
description: Linux应用方向，及个人在对应方向所需要学习的东西
categories:
- Linux
tags:
- Linux
---

我发现在学习Linux后的IT人员，选择开发要比系统应用薪水要上升的更为迅速，而且更容易突破5000、8000 或10000等每月的门槛；但是这个方向难度会更大，同时会更枯燥些，需要毅力和身体，更需要兴趣。如何入门及如何规划自己的Linux学习之路一直是困 扰Linux爱好者的一大难题，实际上，Linux的入门与学习并不难，只是由于长期使用其他操作系统的习惯与没有正确的引导指向，所以一个本身不是难题 的问题变成了难题。

很多同学不知道企业为什么要用linux，为什么要用unix？许多同学认为Linux/unix更好，更稳定，其实不然。企业为什么要用 它们，其实是本着节约成本和创造价值的目的。服务器操作系统的软件投入和服务投入是相当大的经费，这也是现在为什么开源免费的CentOS和 FreeBSD越来越受欢迎的原因之一。再说下硬件的负载均衡器，我说下大家最为熟悉的F5，性能一般的17万；性能越好，价格也水涨船高。但其实许多企 业所需要的Linux集群环境其实并发不是大太，完全可以用开源免费的软件来代替，例如LVS、Nginx，而这些软件一般都是部署在Linux上，这也 是近年来Linux越来越受欢迎的原因之一。

另外，千万不能仅仅学习Linux系统本身。Linux仅仅是操作系统，重要的是其上的应用，就是能为你能创造多少价值。学习Linux的一个误区在于精 力只是局限于Linux本身，从Linux的操作到Linux的内核，也不管自己到底要干嘛；但是系统毕竟是拿来用的，学习使用Linux的目的是创造效 益，如果不是仅仅制作Linux系统，不是加入Red Hat这样的专业Linux发布版厂商，那么我们在学习了Linux基础后就跟掌握了Windows没两样，很显然这是不够的，我们还需要更进一步的学习：选择Linux上的应用，或者Linux上的软件开发，当然还有数据库方向。就这三方面详细说明下：

### Linux的系统、网络、服务、集群、网站、网络应用方向：

1. Web应用服务器，如sina、百度等大型网站
2. Mail应用服务器，如163或外企mail系统等
3. 中间件或J2EE服务器，如为JBOSS Weblogic做平台
4. 网络应用等。

### 嵌入式开发、UNIX/Linux应用系统开发，Linux内核驱动开发方向，主要有以下几类：

1. Linux下的C/C++ 系统程序开发
2. Linux平台Java体系开发和PHP开发
3. Linux下的图形界面开发
4. Linux底层内核/驱动开发
5. 嵌入式Linux开发等。

### Linux下的数据库，如Mysql、oracle和windows下的SQL Server及DB2等。

数据库的重要性我就不重复了，而DBA的作用称得上举足轻重了：在技术类岗位的薪资待遇上，总监之下就是DBA了。51CTO也有专门的数据库频道，编辑在这里推荐一下。

以上是Linux/unix发展的三个方向，大家可以在平时工作和学习中有所偏重，三个专业精通一个即可。注意我这里说的是精通，而非仅仅熟悉的程度。

如果按照地域划分，Linux/unix系统运维方面的工作适合在北京、上海、深圳和广州，开发比较适合在杭州和南京等地区。在武汉，Linux/unix应用比较少，其系统运维方面的工作还真是不好找，在北京、上海那边许多成熟的开源技术，如LVS、CDN，在武汉这边基本是看不到的，甚是遗憾。

我这里想说的是：如果是初学Linux/unix的同学，建议可以以RHEL/CentOS为主，在找工作中尽量不要想到待遇问题，在工作实践中，尽量熟 悉shell和一些基础的网络应用，内网开发服务器的配置环境一定要熟悉；如果有机会出外做项目的，一定要兢兢业业，因为这个是成长得最快最好的时候。另 外，无论是学习还是施工期间，有关环境配置一定要形成文档，尽量写得详细和完善，这样无论是对自身技术提高还是跟同事进行工作交接都是非常有帮助的；如果 没有对外项目的机会，可以写技术型博客，详细记录自己的学习过程。如果想进一步提高自己的水平，建议多上一些跟linux/unix有关的论坛，跟一些同 行或技术大牛交流，这样提高起来也非常快。

如果是资深的linux/unix的系统管理员，建议可以脱离系统级别，向网络架构师的发向发展。网络架构是件艺术活，有时系统性能就是看网站架构师的能 力了。通常情况下，此职位由公司的技术总监担任，它要求技术总监对系统、程序、网络及数据库都有相当的了解。

---

linux 现在用的越来越多，归结起来，主要有两个领域在用linux:
1. 作为服务器；
2. 作为嵌入式系统产品的支持操作系统；

对第一个领域来说，通常可能是要作为WEB服务器来使用。如果你去做这个，需要熟悉譬如Linux, Apache, Mysql, PHP的使用和开发即可。这四个合起来被称为 LAMP。当然，做服务器应用的可能还需要学习类似的，如 java, oracle 之类的技术和软件。总体来说，学习这些东西其实并不难，市场上也已经有很多人在做这方面的事情。所以，假如你现在继续跟进的话，可能将来不会有太大的发展。

对第二个领域，相比第一个领域来说，其挑战和机遇要更多一些。现在各行各业都会用到嵌入式的应用，比方信息家电，医疗电子，通信设备，工业控制等各领域。 另外一个原因，现在做方面的人，虽然比前几年多了一些，但总体上还是供不应求，况且市面上很多人都只是知道一个皮毛，远没有到熟悉或者精通的地步。所以， 我的建议，假如你此刻面临选择，那不如就选这个方向。

当然，进入这第二个领域也比第一个领域要难的多，其要学习掌握远不止是软件层面的东西，它要求你软硬件都懂，上面的应用层和下面的底层也都懂。:) 正是要学的东西多，难度高，所以才注定了现在参与其中的人还不算多。同样的，假如你想完全靠自己自学来进入这个领域，也是颇有困难的，最好的办法就是找到 一个在这方面有着很丰富实际经验的团队，在他们的指导下进行学习，这样将会事半功倍。