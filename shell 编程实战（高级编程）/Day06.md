# `Shell`高级编程 Day06 —— Shell函数的知识与实践
## 1 Shell函数的概念与作用介绍
* 函数的作用就是将程序里多次被调用的相同代码组合起来（函数体），并为其取一个名字（即函数名），其他所有想重复调用这部分代码的地方只需要调用这个名字就可以了。当需要修改这部分重复代码时，只需要改变函数体内的一份代码即可实现对所有调用的修改，也可以把函数独立地写到文件里，当需要调用函数时，再加载进来使用。
* Shell函数的优势整理
  * 把相同的程序段定义成函数，可以减少整个程序的代码量，提升开发效率。
  * 增加程序的可读性、易读性，提升管理效率。
  * 可以实现程序功能模块化，使得程序具备通用性（可移植性）。

## 2 Shell函数的语法
```Shell
    # 其标准写法为；
    function 函数名() {
        指令...
        return n
    }
    # 简化写法1：
    function 函数名 {
        指令...
        return n
    }
    # 简化写法2：
    函数名() {
        指令...
        return n
    }
```

## 3 Shell函数的执行
* Shell的函数分为最基本的函数和可以传参的函数两种
* 1）执行不带参数的函数时，直接输入函数名即可
```shell
    函数名
```
  * 注意：
    * 执行Shell函数时，函数名前的function和函数后的小括号都不要带。
    * 函数的定义必须在要执行的程序前面定义或加载
    * Shell执行系统中各种程序的执行顺序为：系统别名->函数->系统命令->可执行文件。
    * 函数执行时，会和调用它的脚本共用变量，也可以为函数设定局部变量及特殊位置参数。
    * 在shell函数里面，return命令的功能与exit类似，return的作用是退出函数，而exit是退出脚本文件。
    * return语句会返回一个退出值（即返回值）给调用函数的当前程序，而exit会返回一个退出值（即返回值）给执行程序的当前Shell
    * 如果将函数存放在独立的文件中，被脚本加载使用时，需要使用source或“.”来加载。
    * 在函数内一般使用local定义局部变量，这些变量离开函数后就会消失。
* 2）带参数的函数执行方法
```shell
    函数名 参数1 参数2
```
  * 注意：
    * Shell的位置参数（$1、$2...、$#、$*、$?及$@）都可以作为函数的参数来使用。
    * 此时父脚本的参数临时地被函数参数所掩盖或隐藏。
    * $0此时特殊，它仍然是父脚本的名称。
    * 当函数执行完成时，原来的命令行脚本的参数即可恢复。
    * 函数的参数变量是在函数体里面定义的。

## 4 Shell函数的基础实践
```bash
    [root@backup ~]# cat 8_1.sh 
    #!/bin/bash
    herhan(){
            echo "I am herhan."
    }
    function heh(){
            echo "I am heh."
    }
    herhan
    heh
```

## 5 利用Shell函数开发企业级URL检测脚本
```bash
    # 将函数的传参转换成脚本文件命令行传参，判断任意指定的URL是否存在异常
    [root@backup ~]# cat 8_5.sh 
    #!/bin/sh
    . /etc/init.d/functions # 引入系统函数库
    # 帮助函数
    function usage(){
            echo $"usage:$0 url"
            exit 1
    }
    # 检测URL函数
    function check_url(){
            wget --spider -q -o /dev/null --tries=1 -T 5 $1
            # -T 指定超时时间，这里的$1为脚本的参数
            if [ $? -eq 0 ];then
                    action "$1 is yes." /bin/true # 这里的action就是在脚本开头引入系统函数库后调用的
            else
                    action "$1 is no." /bin/false
            fi
    }
    function main(){
            if [ $# -ne 1 ];then
                    usage
            fi
            check_url $1
            # 接收函数的传参，即把下文main结尾的$*传到这里
    }
    # 这里的$*就是把命令行接收的所有参数作为函数参数传给函数内部，是一种常用手法
    main $*
    [root@backup ~]# sh 8_5.sh www.baidu123.com
    www.baidu123.com is no.                                    [失败]
    [root@backup ~]# sh 8_5.sh www.baidu.com
    www.baidu.com is yes.                                      [确定]
```

## 6 利用Shell函数开发一键优化系统脚本
```bash
    # 编写Shell开发Linux系统一键优化脚本
    # 1）思考如何优化Linux系统，并写出来
    # *安装系统时精简安装包
    # *配置国内的告诉yum源
    # *禁用开机不需要启动的服务
    # *优化系统内核参数/etc/sysctl.conf
    # *增加系统文件描述符、堆栈等配置
    # *禁止root远程登录，修改SSH端口为特殊端口，禁止DNS及空密码
    # *有外网IP的机器要开启、配置防火墙，仅对外开启需要提供服务的端口、配置或关闭SELinux。
    # *清除无用的默认系统账户或组（非必须）（添加运维成员的用户）
    # *锁定敏感文件，如/etc/passwd（非必须）
    # *配置服务器和互联网时间同步
    # *初始化用户，并配置sudo对普通用户权限的控制
    # *修改系统字符集
    # *补装系统软件及升级系统到最新
    # 2)
    # 0.更改yum源
    mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup && \
    curl -s -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
    curl -s -o /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo

    # 1.关于SELinux
    sed -i 's/SELINUX=enforing/SELINUX=disabled' /etc/selinux/config
    grep SELINUX=disabled /etc/selinux/config
    setenforce 0
    getenforce

    # 2.关闭firewalld
    systemctl disable firewalld
    systemctl stop firewalld
    systemctl status firewalld

    # 3.精简开机自启动服务
    systemctl list-unit-files | grep enable|egrep -v "sshd.service|crond.service|sysstat|rsyslog|^NetworkManager.service|irqbalance.service"|awk '{print "systemctl disable",$1}'|bash
    systemctl list-unit-files |grep enable

    # 4.提权herhan可以sudo
    useradd herhan
    echo 123456|passwd --stdin herhan
    \cp /etc/sudoers /etc/sudoers.ori
    echo "herhan ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
    tail -l /etc/sudoers
    visudo -c

    # 5.中文字符集
    cp /etc/locale.conf{,.ori}
    localectl set-locale LANG="zh_CN.UTF-8"
    cat /etc/locale.conf

    # 6.时间同步
    yum install ntpdate -y
    /usr/sbin/ntpdate ntp3.aliyun.com
    echo '#crond-id-001:time sync by herhan' >>/var/spool/cron/root
    echo "*/5 * * * * /usr/sbin/ntpdate ntp3.aliyun.com >/dev/null 2>&1">>/var/spool/cron/root
    crontab -l

    # 7.命令行安全
    #echo 'export TMOUT=300' >>/etc/profile
    #echo 'export HISTSIZE=5' >>/etc/profile
    #echo 'export HISTFILESIZE=5' >>/etc/profile
    #tail -3 /etc/profile
    #. /etc/profile

    # 8.加大文件描述
    echo '*         -       nofile          65535 ' >>/etc/security/limits.conf
    tail -l /etc/security/limits.conf

    # 9.内核优化
    cat >>/etc/sysctl.conf<<EOF
    net.ipv4.tcp_fin_timeout = 2
    net.ipv4.tcp_tw_reuse = 1
    net.ipv4.tcp_tw_recycle = 1
    net.ipv4.tcp_syncookies = 1
    net.ipv4.tcp_keepalive_time = 600
    net.ipv4.ip_local_port_range = 4000     65000
    net.ipv4.tcp_max_syn_backlog = 16384
    net.ipv4.tcp_max_tw_buckets = 36000
    net.ipv4.route.gc_timeout = 100
    net.ipv4.tcp_syn_retries = 1
    net.ipv4.tcp_synack_retries = 1
    net.core.somaxconn = 16384
    net.core.netdev_max_backlog = 16384
    net.ipv4.tcp_max_orphans = 16384
    # 以下参数是对iptables防火墙的优化，防火墙不开，会有提示，可以忽略不理
    net.nf_conntrack_max = 25000000
    net.netfilter.nf_conntrack_max = 25000000
    net.netfilter.nf_conntrack_tcp_timeout_established = 180
    net.netfilter.nf_conntrack_tcp_timeout_time_wait = 120
    net.netfilter.nf_conntrack_tcp_timeout_close_wait = 60
    net.netfilter.nf_conntrack_tcp_timeout_fin_wait = 120
    EOF
    sysctl -p
    yum update -y
    yum install tree nmap dos2unix lrzsz nc lsof wget tcpdump htop iftop iotop sysstat nethogs -y
    yum install psmisc net-tools bash-completion vim-enhanced -y
    
    # 3) 将每个优化模块写成函数
    [root@backup ~]# cat sys_opt.sh 
    #!/bin/bash
    # author:herhan
    #set env
    export PATH=$PATH:/bin:/sbin:/usr/sbin
    # Require root to run this script.
    if [ "$UID" != "0" ];then
            echo "Please run this script by root."
            exit 1
    fi

    # define cmd var
    SERVICE=`which service`
    CHKCONFIG=`which chkconfig`


    # 0.更改yum源
    function mod_yum(){
            #modify yum path
            if [ -e /etc/yum.repos.d/CentOS-Base.repo ];then
                    mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup && \
                    wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
                    wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
            fi
    }

    # 1.关于SELinux
    function close_selinux(){
            #1.close selinux
            sed -i 's/SELINUX=enforing/SELINUX=disabled' /etc/selinux/config
            #grep SELINUX=disabled /etc/selinux/config
            setenforce 0 &>/dev/null
            #getenforce
    }


    # 2.关闭firewalld
    function close_firewalld(){
            #2.close firewalld
            systemctl disable firewalld
            systemctl stop firewalld
            # systemctl status firewalld
    }

    # 3.精简开机自启动服务
    function least_service(){
            #3.least service startup
            systemctl list-unit-files | grep enable|egrep -v "sshd.service|crond.service|sysstat|rsyslog|^NetworkManager.service|irqbalance.service"|awk '{print "systemctl disable",$1}'|bash
            #systemctl list-unit-files |grep enable
    }

    # 4.提权herhan可以sudo
    function adduser(){
            #4.add herhan and sudo
            if [ `grep -w herhan /etc/passwd|wc -l` -lt 1 ];then
                    useradd herhan
                    echo 123456|passwd --stdin herhan
                    \cp /etc/sudoers /etc/sudoers.ori
                    echo "herhan ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
                    tail -l /etc/sudoers
                    visudo -c &>/dev/null
            fi
    }

    # 5.中文字符集
    function charset(){
            #5.charset config
            cp /etc/locale.conf{,.ori}
            localectl set-locale LANG="zh_CN.UTF-8"
            #cat /etc/locale.conf
    }

    # 6.时间同步
    function time_sync(){
            #6.time sync.
            cron=/var/spool/cron/root
            if [ `grep -w "ntpdate" $cron|wc -l` -lt 1 ];then
                    yum install ntpdate -y
                    #/usr/sbin/ntpdate ntp3.aliyun.com
                    echo '#crond-id-001:time sync by herhan' >>$cron
                    echo "*/5 * * * * /usr/sbin/ntpdate ntp3.aliyun.com >/dev/null 2>&1">>$cron
                    crontab -l
            fi
    }

    # 7.命令行安全
    function com_line_set(){
            #7.command set
            if [ `egrep "TMOUT|HISTSIZE|HISTFILESIZE" /etc/profile|wc -l` -lt 3 ];then
                    echo 'export TMOUT=300' >>/etc/profile
                    echo 'export HISTSIZE=5' >>/etc/profile
                    echo 'export HISTFILESIZE=5' >>/etc/profile
                    #tail -3 /etc/profile
                    . /etc/profile
            fi
    }

    # 8.加大文件描述
    function open_file_set(){
            #8.increase open file.
            if [ `grep 65535 /etc/security/limits.conf|wc -l` -lt 1 ];then
                    echo '*         -       nofile          65535 ' >>/etc/security/limits.conf
                    tail -l /etc/security/limits.conf
            fi
    }

    # 9.内核优化
    function set_kernel(){
            #9.kernel set.
            if [ `grep kernel_flag /etc/sysctl.conf|wc -l` -lt 1 ];then
                    cat >>/etc/sysctl.conf<<EOF
                    #kernel_flag
                    net.ipv4.tcp_fin_timeout = 2
                    net.ipv4.tcp_tw_reuse = 1
                    net.ipv4.tcp_tw_recycle = 1
                    net.ipv4.tcp_syncookies = 1
                    net.ipv4.tcp_keepalive_time = 600
                    net.ipv4.ip_local_port_range = 4000     65000
                    net.ipv4.tcp_max_syn_backlog = 16384
                    net.ipv4.tcp_max_tw_buckets = 36000
                    net.ipv4.route.gc_timeout = 100
                    net.ipv4.tcp_syn_retries = 1
                    net.ipv4.tcp_synack_retries = 1
                    net.core.somaxconn = 16384
                    net.core.netdev_max_backlog = 16384
                    net.ipv4.tcp_max_orphans = 16384
                    # 以下参数是对iptables防火墙的优化，防火墙不开，会有提示，可以忽略不理
                    net.nf_conntrack_max = 25000000
                    net.netfilter.nf_conntrack_max = 25000000
                    net.netfilter.nf_conntrack_tcp_timeout_established = 180
                    net.netfilter.nf_conntrack_tcp_timeout_time_wait = 120
                    net.netfilter.nf_conntrack_tcp_timeout_close_wait = 60
                    net.netfilter.nf_conntrack_tcp_timeout_fin_wait = 120
    EOF
                    sysctl -p
            fi
    }

    function init_ssh(){
            \cp /etc/ssh/sshd_config /etc/ssh/sshd_config.`date +"%Y-%m-%d_%H-%M-%S"`
            # sed -i 's%#Port 22%Port 52113%' /etc/ssh/sshd_config
            sed -i 's%#PermitRootLogin yes%PermitRootLogin no%' /etc/ssh/sshd_config
            sed -i 's%#PermitEmptyPasswords no%PermitEmptyPasswords no' /etc/ssh/sshd_config
            sed -i 's%#UseDNS yes%UseDNS no%' /etc/ssh/sshd_config
            systemctl restart sshd
    }

    function update_linux(){
            #10.upgrade linux.
            if [ `rpm -qa tree nmap dos2unix lrzsz nc lsof wget tcpdump htop iftop iotop sysstat nethogs psmisc net-tools bash-completion vim-enhanced|wc -l` -le 3 ];then
                    yum update -y
                    yum install tree nmap dos2unix lrzsz nc lsof wget tcpdump htop iftop iotop sysstat nethogs -y
                    yum install psmisc net-tools bash-completion vim-enhanced -y
            fi
    }

    main(){
        mod_yum
        close_selinux
        close_firewalld
        least_service
        adduser
        charset
        time_sync
        com_line_set
        open_file_set
        set_kernel
        init_ssh
        update_linux
    }
    main
```