# Linux-day1

## 1. Linux 是什么

* Liunx 就是一套操作系统，它提供了一个完整的操作系统当中最底层的硬件控制与资源管理的完整架构，这个架构是沿用Unix良好的传统的而来的，所以相当稳定并且功能强大。

### 1.1 Linux的历史

* 众所周知，Linux的内核是由`Linux Torvalds`在1991年的时候给开发出来的。

* 1969年以前：一个伟大的梦想--`Bell、MIT`与`GE`的`"Multics"`系统。
  * 因为那个时候的输入设备只有卡片阅读机，输出设备是有打印机，用户也无法与操作系统互动（多道批处理操作系统）。

  * 20世纪60年代`MIT`开发了分时操作系统（`Compatible Time-Sharing System,CTSS`）,他可以让大型主机通过提供多个终端机（`Terminal`）以连接进入主机，从而利用主机的资源进行运算工作。

  * 1965年前后，由贝尔实验室（`Bell`）、麻省理工学院（`MIT`）及通用电气公司（`GE`）共同发起了`Multics`的项目，该项目目的是向要让大型主机可以同时提供300台以上的终端机连接使用的目标。

* 1969年：`Ken Thompson`的小型 `File Server System`

  * 该系统是`UNIX`的原型，以汇编语言（`Assembler`）写出了一组内核程序，同时包括一些内核工具程序，以及一个小的文件系统。

  * `Thompson`的文件系统有两个重要的概念
    * 所有的程序或系统装置都是文件。
    * 不管构建编辑器还是附属文件，所写的程序只有一个目的，就是有效的完成目标。

* 1973年：`UNIX`正式诞生，`Ritchie`等人以C语言写出第一个正式UNIX内核
  * 由于`UNIX`是以较高级的C语言编写的，相对于汇编语言需要与硬件有密切的配合，C语言与硬件的相关性就没有这么大，所以，这个改变也使得`UNIX`很容易被移植到不同的机器上。

* 1977年：重要的`UNIX`分支——`BSD`的诞生
  * 柏克莱大学的`Bill Joy`在取得了`UNIX`的内核源码后，着手修改成适合自己机器的版本，并且同时增加了很多工具软件与编译程序，最终将它命名为`Berkeley Software Distribution(BSD)`。

* 1979年：重要的`System V`架构与版权声明
  * 操作系统的内核必须要跟硬件配合，以提供及控制硬件的资源进行良好的工作。

  * 早先的`UNIX`只能与服务器（`Server`）或者是大型工作站（`Workstation`）划上等号

  * 可以支持x86架构的个人计算机系统，也就是说`System V`可以在个人计算机上面安装与运行了。

  * 不可对学生提供源码。

* 1984年之一：x86架构的`Minix`操作系统诞生
  * 既然1979年的`UNIX`第七版可以在`Intel`的x86架构上面进行移植，那么是否意味着可以将`UNIX`改写并移植到x86上面了呢？

  * 购买时，随磁盘还会附上`Minix`的源码！这个特色对于`Linux`的开始开发阶段，可是有很大的关系。

* 1984年之二：`GUN`项目与`FSF`基金会成立
  * 一个分享的环境

  * 使用`UNIX`开发环境

  * `GUN`项目的推展
    * 这个项目的目的是创建一个自由、开放的`UNIX`操作系统（`Free UNIX`）

    * 史托曼便借着`Emacs`以磁带出售，赚了以点钱，从而开始全力编写其他软件，并且成立自由软件基金会（`Free Software Foundation,FSF`）,请更多工程师与志愿者来编写软件。

  * `GNU`的通用公共许可证
    * 通用公共许可证（`General Public Licenxe,GPL`）,并且称呼它为`CopyLeft`。

    * 由`GNU`所开发的几个重要软件：
      * `Emacs`
      * `GNU C（GCC）`
      * `GNU C Library(GLIBC)`
      * `Bash shell`

  * 1988年：图形接口`XFree86`项目
    * `X Window System + Free + x86`

  * 1991年：芬兰大学生`Linus Torvalds`的一则消息。