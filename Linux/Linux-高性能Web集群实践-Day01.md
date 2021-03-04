<!--
 * @Author: your name
 * @Date: 2020-12-01 23:03:12
 * @LastEditTime: 2020-12-07 00:12:43
 * @LatEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \notebook\Linux\Linux-高性能Web集群实践.md
-->

Linux-高性能Web集群实践
===

<!-- TOC -->

- [Linux-高性能Web集群实践](#linux-高性能web集群实践)
- [Day 01 实践试验准备阶段](#day-01-实践试验准备阶段)
  - [1.试验镜像](#1试验镜像)
  - [2.修改网卡名为`eth0`的形式](#2修改网卡名为eth0的形式)
  - [3.我采用`Minimal Install`,并且需要安装一下几个包组:](#3我采用minimal-install并且需要安装一下几个包组)
  - [4.企业生产场景中`Linux`系统的分区方案](#4企业生产场景中linux系统的分区方案)
  - [5.系统无法联网的故障排除方法](#5系统无法联网的故障排除方法)
  - [6.更新系统](#6更新系统)
  - [7.额外安装一些有用的软件包](#7额外安装一些有用的软件包)

<!-- /TOC -->
# Day 01 实践试验准备阶段

## 1.试验镜像

* [CentOS7.6](https://vault.centos.org3/7.6.1810/isos/x86_64/)

* 提示:安装`Linux`系统的常见引导方式有如下几种:
  * 光盘引导安装(不推荐)
  * U盘引导安装(机器数量少时推荐)
  * 网络安装(需要网卡支持,现在主流网卡都支持,机器数量多时推荐使用此方法).

* 32位和64位系统到底有什么不同
  * 区别一:设计定位不同
    * 是否需要大量的系统内存和浮点性能
    * 64位操作系统是为高科技人员使用本行业特殊软件的运行平台而设计的
    * 32位操作系统是为普通用户设计的
  * 区别二:安装要求配置不同
    * CPU的配置要求
  * 区别三:运算速度不同
    * 64位CPU GPR(General-Purpose Register,通用寄存器)的数据宽度为64位
    * 处理器一次可提取64位数据(只要两个指令,一次提取8个字节的数据),比32位提高了一倍(32位需要4个指令,一次只能提取4个字节的数据)
  * 区别四:寻址
  * 如何区分已安装的系统是32位还是64位
    * 方法1

    ```shell
      [root@www ~]# uname -m
      x86_64
      [root@www ~]# uname -a
      Linux www 3.10.0-957.el7.x86_64 #1 SMP Thu Nov 8 23:39:32 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux 
    ```

    * 方法2

    ```shell
      [root@www ~]# ls -d /lib64
      /lib64
    ```

## 2.修改网卡名为`eth0`的形式

* CentOS7 以前版本的`ethX`数字编号的网卡命名往往不一定准确对应网卡接口的物理顺序,CentOS7的网卡默认命名可以解决这一问题,但同时也给Linux系统管理员带来了其他的困扰,就是无法兼容CentOS6的环境以及使用习惯,所以,一般还会改回早期的eth0,eth1网卡命名形式.
* 具体的内核参数修改组合如下,
  * 默认内核参数(`biosdevname=0,net.ifnames=1`):网卡名如"`enp5s2`";
  * 如果`biosdevname=1,net.ifnames=0`:网卡名如"`em1`";
  * 如果`biosdevname=0,net.ifnames=0`:网卡名如"`eth0`"(传统的方式)
  
![1-1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/web/1.1.png)

## 3.我采用`Minimal Install`,并且需要安装一下几个包组:
  
* 安装"`Compatibility libraries`","`Base`","`Development tools`" 3个包组

  ```shell
    yum group install "Compatibility libraries" "Base" "Development tools"
  ```
  
* 安装"`Debugging Tools`","`Dial-up Networking Support`"

  ```shell
    yum group install "debugging Tools","Dial-up Networking Support"
  ```

* 查看具体安装的包组组件

  ```shell
    yum groupinfo 包组名 
  ```

## 4.企业生产场景中`Linux`系统的分区方案
  
* 方案1:针对网站集群架构中的某个节点服务器分区,该服务器上的数据有多份(其他节点也有)且数据不太重要,建议的分区方案如下:
  * `/boot`:设置为512~1024MB
  * `swap`:为物理内存的1.5倍,当内存大于等于8GB时,分配8~16GB即可
  * `/`:剩余硬盘空间大小(`/usr`,`/home`,`/var`等分区"`/`"共用一个分区,这相当于在Windows系统中只有一个C盘,所有数据和系统文件都放在一起)

* 方案2:针对数据库及存储角色的服务器分区,该服务器的业务包含大量重要的数据,建议分区方案如下:
  * `/boot`:设置为512~1024MB
  * `/`:大小设置为50~200GB,只存放系统相关文件,网站等业务数据不放在这里
  * `swap`:为物理内存的1.5倍,当内存大于等于8GB时,分配8~16GB即可
  * `/data`:剩余硬盘空间大小,存放数据库及存储服务等重要数据
  * 本方案其实就是将重要数据单独分区,便于备份和管理
  
* 方案3:针对大网站或门户级企业的服务器进行分区
  * `/boot`:大小设置为512~1024MB
  * `swap`:为物理内存的1.5倍,当内存大于等于8GB时,分配8~16GB即可
  * `/`:大小设置为50~200GB,只存放系统相关文件,网站等业务数据不放在这里
  * 保留剩余的磁盘空间,不再进行分区,将来它们分配给不同的使用部门,由他们自己根据需求再分
  * 此种分区方案更灵活,比较适合业务线比较多,需求不确定的大企业使用

## 5.系统无法联网的故障排除方法

* (1)首先要确认系统的安装方式:是虚拟机还是物理服务器
* (2)如果系统安装方式无误,仍然不能上网,继续确认网卡实际配置是否正确
* (3)上面两类问题都排除后,就需要确认网卡的配置文件了

## 6.更新系统
  
```shell
  [root@www ~]# yum update -y
```

## 7.额外安装一些有用的软件包

* 安装的企业运维常用基础工具包1如下:
  
  ```shell
    yum install tree nmap dos2unix lrzsz nc lsof wget tcpdump htop iftop iotop sysstat nethogs -y
  ```

  工具包名|包含的核心命令
  -|-
  `tree`|以树形结构显示文件和目录
  `nmap`|扫描端口的工具
  `dos2unix`|转换脚本格式的工具
  `lrzsz`|包含上传(`rz`),下载(`sz`)文件的工具
  `nc`|文件传输,端口检查工具
  `lsof`|反查端口进程,以及服务开发文件工具
  `wget`|下载软件包工具
  `tcpdump`|抓包,监听等重要排错工具
  `htop`|系统进程相关信息查看工具
  `iftop`|查看主机网卡带宽的工具
  `sysstat`|`sar`,`iostat`等重要系统性能查看工具
  `nethogs`|显示进程的网络流量

* 安装的企业运维常用基础工具包2如下:

  ```shell
    yum install psmisc net-tools bash-completion vim-enhanced -y
  ```

  工具包名|包含的核心命令
  -|-
  `psmisc`|含有`killall`,`pstree`等命令
  `net-tools`|含有`netstat`,`ifconfig`,`route`,`arp`等命令
  `bash-completion` `bash-completion-extras`|tab补全功能工具包
  `vim-enhanced`|`vim`编辑器工具包
  