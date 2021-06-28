# `Shell`高级编程 Day03

## 1.什么是Shell变量
### 1.1 什么是变量 
* 变量就是用一个固定的字符串代替更多、更复杂的内容，该内容里可能还会包含变量、路径、字符串等其他的内容。
* 变量是暂时存储数据的地方及数据标记，所存储的数据存在于内存空间中，通过正确地调用内存空间中变量的名字就可以取出与变量对应的数据。
* 定义变量和打印变量：
  ```shell
    [root@herhan ~]# herhan="I am herhan"
    [root@herhan ~]# echo $herhan
    I am herhan
  ```
  * 变量的赋值方式为：先写变量名称，紧接着是“=”这个字符，最后是值，**中间无任何空格**。

### 1.2 Shell变量的特性
* 默认情况下，在Bash Shell中是不会区分变量类型的
* 可以使用declare显示定义变量的类型，但在一般情况下没有这个需求。

### 1.3 变量类型
* 分为两类：环境变量（全局变量）和普通变量（局部变量）
* 环境变量也可称为全局变量，可以在创建它们的Shell及其派生出来的任意子进程Shell中使用，环境变量又可分为自定义环境变量和bash内置的环境变量
* 普通变量也可称为局部变量，只能在创建它们的Shell函数或Shell脚本中使用。普通变量一般由开发者在开发脚本程序时创建。

## 2.环境变量
* 环境变量一般是指用export内置命令导出的变量，用于定义Shell的运行环境，保证Shell命令的正确执行
* 环境变量在用户退出命令行时会丢失
* 永久保存环境变量：在用户家目录下的`.bash_porfile`或`.bashrc`文件中，或者全局配置`/etc/bashrc`或`/etc/profile`文件中定义。在将环境变量放入上传文件中后，每次用户登录时这些变量都将被初始化。
* 环境变量的名字均采用大写形式。
* 用export命令导出定义：`export OLDGIRL=1`
* 常见的环境变量：
    变量名|含义
    -|-
    `_=`|上一条命令的最后一个参数
    `BASH=/bin/bash`|调用bash实例时使用的全路径名
    `BASH_VERSINFO=([0]="3"[1]="2"[2]="25"[3]="1"[4]="release"[5]="x86_64-redhat-linux-gnu")`|使用2.0以上版本时，展开为版本信息
    `BASH_VERSION='3.2.25(1)-release'`|当前bash实例的版本号
    `COLORS=/etc/DIR_COLORS`|颜色变量
    `COLUMNS=132`|设置该变量，就给shell编辑模式和选择的命令定义了编辑窗口的宽度
    `DIRSTACK=()`|代表目录栈的当前内容
    `EUID=0`|在shell启动时被初始化的当前用户的有效ID
    `GROUPS=()`|当前用户所属的组
    `HISTFILE=/root/.bash_history`|历史记录文件的全路径
    `HISTFILESIZE=50`|历史文件能包含的最大行数
    `HISTSIZE=50`|记录在命令行历史文件中的命令行数
    `HOME=/root`|当前用户家目录
    `HOSTNAME=herhan`|当前主机名称
    `HOSTTYPE=x86_64`|当前操作系统类型
    `IFS=S'\t\n'`|内部字段分隔符，一般是空格符、制表符和换行符、用于划分由命令替换、循环结构中的表和所读取的输入产生的词的字段
    `INPUTRC=/etc/inputrc`|readline启动文件的文件名，取代默认的~/.inputrc
    `LANG=zh_CN.UTF-8`|字符集
    `LOGNAME=root`|登录用户名称
    `MACHTYPE=x86_64-redhat-linux-gnu`|包含一个描述正在运行bash的系统串
    `MAILCHECK=60`|这个参数定义Shell将隔多长时间（以秒为单位）检查一次由参数MAILPATH或MAILFILE指定的文件，看看是否有邮件到达。默认值为600s
    `MAIL=/var/spool/mail/root`|邮件全路径
    `OLDPWD=/root`|前一个当前工作目录
    `OPTIND=1`|下一个由getopts内置命令处理的参数的序号
    `OSTYPE=linux-gnu`|自动设置成一个串，该串描述正在运行bash的操作系统，默认值由系统来决定
    `PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin`|全局PATH路径，命令搜索路径。一个由冒号分隔的目录列表，Shell用它来搜索命令。默认路径由系统来决定，并且由安装bash的管理员来设置
    `PIPESTATUS=([0]="0"[1]="0")`|一个数组，包含一列最近在管道执行的前台作业的进程退出的状态值
    `PPID=1112`|父进程的进程ID
    `PS1=[\u@\h \W]\$`|主提示符串，默认值是$
    `PS2='>'`|次提示符串，默认值是>
    `PS4='+'`|开启追踪时使用的是调试提示符串，默认值是+，追踪可用set-x开启
    `PWD=/home`|当前用户家目录
    `RESIN_HOME=/application/resin-3.1.6`|这是通过export人为设置的环境变量
    `SHELL=/bin/bash`|登录Shell类型
    `SHELLOPTS=braceexpand:emacs:hashall:histexpand:history:interactive-comments:monitor`|包含一列开启的Shell选项
    `SHLVL=1`|每启动一个bash实例就将其加1
    `TERM=vt100`|终端设置
    `TMOUT=3600`|退出前等待超时的秒数
    `UID=0`|当前用的UID，在Shell启动时初始化
    `USER=root`|当前用户的用户名，在Shell启动时初始化

* 显示环境变量：`set`、`env`和`declare`
  * `set`命令输出所有的变量，包含全局变量和局部变量
    * `set -o`命令显示bash Shell的所有参数配置信息
  * `env`命令只显示全局变量
  * `declare`命令输出所有的变量、函数、整数和已经导出的变量
  
  ```bash
    # 范例3-1
    # env
    [root@herhan ~]# env|tail
    PWD=/root
    LANG=en_US.UTF-8
    HISTCONTROL=ignoredups
    SHLVL=1
    HOME=/root
    LOGNAME=root
    SSH_CONNECTION=219.144.184.70 19397 172.30.199.136 22
    LESSOPEN=||/usr/bin/lesspipe.sh %s
    XDG_RUNTIME_DIR=/run/user/0
    _=/usr/bin/env

    # declare
    [root@herhan ~]# set -o|head
    allexport       off
    braceexpand     on
    emacs           on
    errexit         off
    errtrace        off
    functrace       off
    hashall         on
    histexpand      on
    history         on
    ignoreeof       off
  ```

### 2.1 自定义环境变量
* 1.设置环境变量
  * export命令和declare命令
    * `export 变量名=value`
    * `变量名=value;export 变量名`
    * `declare -x 变量名=value`