# kvm介绍
* 虚拟化技术的演变过程（三大阶段）
  * 软件模拟：
    * 定义：通过软件完全模拟CPU、芯片组、磁盘、网卡等计算机硬件）
    * 代表软件：QEMU
    * 架构：QEMU虚拟机架构
    ![kvm-1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/kvm/kvm-1.jpg)
    * 缺点：非常低效
  * 虚拟化层翻译
    * 软件捕获翻译，即软件全虚拟化。
      * 定义：虚拟机上虽然使用的是物理机一样的指令，但是虚拟机不能对硬件进行操作的原因，比如重启虚拟机不会引起宿主机的重启，这种解决方案叫软件全虚拟化方案。
      * 代表软件：VMwave
      * 架构：
    ![kvm-2](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/kvm/kvm-2.jpg)
    * 改造虚拟机系统内核加虚拟化层翻译。即半虚拟化。
      * 代表软件：Xen
      * 优点：效率高
      * 缺点：使用有限制、配置比较麻烦
    * 硬件支持的虚拟化层翻译，即硬件支持的全虚拟化。
      * 代表方案：VT-x两种操作模式：VMX root operation和 VMX non-root operation。两种操作模式都支持Ring0~Ring3这4个特权级。
      * 架构：硬件虚拟化CPU指令说明
    ![kvm-3](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/kvm/kvm-3.jpg)
            硬件全虚拟化方案
    ![kvm-4](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/kvm/kvm-4.jpg)
      * 优点：效率非常高
      * VT-d：I/O通讯硬件解决方案
      * VT-c：网络通讯硬件解决方案
  * 容器虚拟化
    * 原理：基于CGroups、Namespace等技术将进程隔离，每个进程就像一台单独的虚拟机一样，有自己被隔离出来的资源，也有自己的根目录、独立的进程编号、被隔离的内存空间。基于容器的虚拟化可以实现在单一内核上运行多个实例。
    * 优点：更高效率
    * 缺点：磁盘、网络性能上还受到很多限制。
    * 代表软件：Docker

* kvm包含一个为处理器提供底层虚拟化、可加载的核心模块kvm.ko(kvm-intel.ko或kvm-amd.ko),使用QEMU作为虚拟机上层控制工具。
* kvm的架构
![kvm-5](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/kvm/kvm-5.jpg)

* QEMU与KVM
  * QEMU 硬件模拟器，纯软件模拟
  * 使用QEMU的管理工具管理KVM

* Libvirt与KVM
  * Libvirt是一套开源的虚拟化管理工具，3部分组成
    * 一套API的lib库，支持主流的编程语言，包括C、Python、Ruby等
    * Libvirtd服务
    * 命令行工具virsh
  * 设计目标是通过相同的方式管理不同的虚拟化引擎，但实际大多数Libvirt管理KVM
  * Libvirt功能
    * Libvirt实现对虚拟机的管理
    * Libvirt支持远程的宿主机管理，基于SSH、TCP、TLS
  * Libvirt的管理
    * 存储池资源管理，支持本地文件系统目录、裸设备、lvm、nfs、iscsi等方式，虚拟机磁盘格式上支持qcow2、vmdk、raw等格式。
    * 网络资源管理，支持Linux桥、VLAN、多网卡绑定管理、Open vSwitch、nat、路由模式

* 容易实现的功能：
  * 资源使用自动化和自助服务
  * 资源池可随时扩展
  * 资源使用标准化
  * 资源使用保持了更好的兼容性

