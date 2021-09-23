<!-- vscode-markdown-toc -->
* 1. [1.什么是Shell变量](#Shell)
	* 1.1. [1.1 什么是变量](#)
	* 1.2. [1.2 Shell变量的特性](#Shell-1)
	* 1.3. [1.3 变量类型](#-1)
* 2. [2.环境变量](#-1)
	* 2.1. [2.1 自定义环境变量](#-1)
	* 2.2. [2.2 显示与取消环境变量](#-1)
	* 2.3. [2.3 环境变量知识小结](#-1)
	* 2.4. [2.4 环境变量初始化与对应文件的生效顺序](#-1)
* 3. [3.普通变量](#-1)
	* 3.1. [3.1 定义本地变量](#-1)
	* 3.2. [3.2 变量定义及变量输出说明](#-1)
* 4. [4.变量定义技巧总结](#-1)
* 5. [5.Shell中特殊且重要的变量](#Shell-1)
	* 5.1. [5.1 shell中特殊位置参数变量](#shell)
	* 5.2. [5.2 Shell进程中的特殊状态变量](#Shell-1)
* 6. [6.bash Shell内置变量命令](#bashShell)
	* 6.1. [6.1 echo 在屏幕上输出信息](#echo)
	* 6.2. [6.2 eval](#eval)
	* 6.3. [6.3 exec](#exec)
	* 6.4. [6.4 read](#read)
	* 6.5. [6.5 shift](#shift)
	* 6.6. [6.5 exit](#exit)
* 7. [7.Shell变量子串知识及实践](#Shell-1)
	* 7.1. [7.1 Shell变量子串介绍](#Shell-1)
	* 7.2. [7.2 Shell变量子串的实践](#Shell-1)
	* 7.3. [7.3 变量子串的生产场景应用案例](#-1)
* 8. [8.Shell特殊扩展变量的知识与实践](#Shell-1)
	* 8.1. [8.1 shell特殊扩展变量介绍](#shell-1)
	* 8.2. [8.2 shell特殊扩展变量的实践](#shell-1)
	* 8.3. [8.3 Shell特殊扩展变量的生产场景应用案例](#Shell-1)
* 9. [9.变量的数值计算实践](#-1)
	* 9.1. [9.1 算术运算符](#-1)
	* 9.2. [9.2 双小括号"(())"数值运算命令](#-1)
	* 9.3. [9.3 let运算命令的用法](#let)
	* 9.4. [9.4 expr命令的用法](#expr)
	* 9.5. [9.5 bc命令的用法](#bc)
	* 9.6. [9.6 awk实现计算](#awk)
	* 9.7. [9.7 declare(同typeset)命令用法](#declaretypeset)
	* 9.8. [$[]符号的运算示例](#-1)
* 10. [10 基于Shell变量输入read命令的运算实践](#Shellread)
	* 10.1. [10.1 read命令基础](#read-1)
	* 10.2. [10.2 以read命令读入及传参的综合企业案例](#read-1)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc --># `Shell`高级编程 Day03

##  1. <a name='Shell'></a>1.什么是Shell变量

###  1.1. <a name=''></a>1.1 什么是变量

* 变量就是用一个固定的字符串代替更多、更复杂的内容，该内容里可能还会包含变量、路径、字符串等其他的内容。
* 变量是暂时存储数据的地方及数据标记，所存储的数据存在于内存空间中，通过正确地调用内存空间中变量的名字就可以取出与变量对应的数据。
* 定义变量和打印变量：
  ```shell
    [root@herhan ~]# herhan="I am herhan"
    [root@herhan ~]# echo $herhan
    I am herhan
  ```
  * 变量的赋值方式为：先写变量名称，紧接着是“=”这个字符，最后是值，**中间无任何空格**。

###  1.2. <a name='Shell-1'></a>1.2 Shell变量的特性

* 默认情况下，在Bash Shell中是不会区分变量类型的
* 可以使用declare显示定义变量的类型，但在一般情况下没有这个需求。

###  1.3. <a name='-1'></a>1.3 变量类型

* 分为两类：环境变量（全局变量）和普通变量（局部变量）
* 环境变量也可称为全局变量，可以在创建它们的Shell及其派生出来的任意子进程Shell中使用，环境变量又可分为自定义环境变量和bash内置的环境变量
* 普通变量也可称为局部变量，只能在创建它们的Shell函数或Shell脚本中使用。普通变量一般由开发者在开发脚本程序时创建。

##  2. <a name='-1'></a>2.环境变量

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

###  2.1. <a name='-1'></a>2.1 自定义环境变量

* 1.设置环境变量
  * export命令和declare命令
    * `export 变量名=value`
    * `变量名=value;export 变量名`
    * `declare -x 变量名=value`
  ```bash
    # 范例：定义环境变量并赋值的方法
    [root@herhan ~]# export NAME=herhna
    [root@herhan ~]# echo $NAME
    herhna
    [root@herhan ~]# declare -x NAME=herhan
    [root@herhan ~]# echo $NAME
    herhan
    [root@herhan ~]# NAME=1herhan;export NAME
    [root@herhan ~]# echo $NAME
    1herhan
  ```
  * 环境变量永久生效的常用设置
    * （1）用户的环境变量配置，在用户家目录下的`.bashrc`和`.bash_profile`
    * （2）全局黄精变量的配置，常见的配置文件`/etc/profile`和`/etc/bashrc`
      * 若要在登录后初始化或显示加载内容，则把脚本文件放在`/etc/profile.d/`下即可

  * 设置登录提示的两种方式
    * （1）在`/etc/motd`里增加提示的字符串
    * （2）在`/etc/profile.d/`下面增加脚本

###  2.2. <a name='-1'></a>2.2 显示与取消环境变量

* 1.通过`echo`或`printf`命令打印环境变量
  * 常见的系统环境变量
    * $HOME：用户登录时进入的目录
    * $UID：当前用户的UID（用户标识），相当于id-u
    * $PWD：当前工作目录的绝对路径名
    * $SHELL：当前SHELL
    * $USER：当前用户
  
  ```bash
    # 通过echo和printf命令打印环境变量
    [root@herhan ~]# echo $HOME
    /root
    [root@herhan ~]# echo $UID
    0
    [root@herhan ~]# echo $SHELL
    /bin/bash
    [root@herhan ~]# echo $USER
    root
    [root@herhan ~]# printf "$HOME\n"
    /root
  ```

* 2.用`env`或`set`显示默认的环境变量
  
  ```bash
    # 用env（printenv）显示默认环境变量的示例如下：
    [root@herhan ~]# env
    XDG_SESSION_ID=96
    HOSTNAME=herhan
    SHELL=/bin/bash
    TERM=xterm-256color
    HISTSIZE=1000
    SSH_CLIENT=117.32.138.251 6606 22
    SSH_TTY=/dev/pts/0
    USER=root
    ...
    
    # 用set也可以显示环境变量（包含局部变量）
    [root@herhan ~]# set
    BASH=/bin/bash
    BASHOPTS=checkwinsize:cmdhist:expand_aliases:extquote:force_fignore:histappend:hostcomplete:interactive_comments:progcomp:promptvars:sourcepath
    BASH_ALIASES=()
    BASH_ARGC=()
    BASH_ARGV=()
    BASH_CMDS=()
    BASH_LINENO=()
    BASH_SOURCE=()
    BASH_VERSINFO=([0]="4" [1]="2" [2]="46" [3]="2" [4]="release" [5]="x86_64-redhat-linux-gnu")
    BASH_VERSION='4.2.46(2)-release'
    ...
  ```

* 3.用`unset`消除本地变量和环境变量
  ```bash
    [root@herhan ~]# echo $USER
    root
    [root@herhan ~]# unset $USER
    [root@herhan ~]# echo $USER
  
  ```

###  2.3. <a name='-1'></a>2.3 环境变量知识小结

* 变量名通常要大写
* 变量可以在自身的Shell及子Shell中使用
* 常用export来定义环境变量
* 执行env默认可以显示所有的环境变量名称及对应的值
* 输出时用“$变量名”，取消时用“unset变量名”
* 书写crond定时任务时要注意，脚本要用到的环境变量最好先在所执行的Shell脚本中重新定义
* 如果希望环境变量永久生效，则可以将其放在用户环境变量文件或全局变量文件里

###  2.4. <a name='-1'></a>2.4 环境变量初始化与对应文件的生效顺序


* 系统环境文件：在登录Linux系统并启动一个bash shell时，默认情况下bash会在若干个文件中查找环境变量的设置，这些文件称为系统环境文件。
* bash检查的环境变量文件的情况取决于系统运行Shell的方式
  * 1）通过系统用户登录后默认运行的Shell
  * 2）非登录交互式运行Shell
  * 3）执行脚本运行非交互式Shell
* 第一种方式下的Shell加载环境变量的顺序
  ![3-1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/shell%E7%BC%96%E7%A8%8B%E5%AE%9E%E6%88%98/3.1.png)
* 第二种方式下的Shell加载环境变量的顺序
  * 非登录Shell只会加载`$HOME/.bashrc`(用户环境变量文件)，并会去找`/etc/bashrc`(全局环境变量文件)。

##  3. <a name='-1'></a>3.普通变量

###  3.1. <a name='-1'></a>3.1 定义本地变量

* 本地变量在用户当前Shell生存期的脚本中使用
* 1.普通变量定义
  * 1）`变量名=value`
    * 是不加任何引号直接定义变量的内容，当内容为简单连续的数字、字符串、路径名时，可以这样用。
    * 不加引号时，值里有变量的会被解析后再输出。
  * 2）`变量名='value'`
    * 这个方式的特点是：输出变量内容时单引号里是什么就输出什么，即使内容中有变量和命令（命令需要反引起来）也会把它们原样输出。这种方式比较适合于定义显示纯字符串的情况。
  * 3）`变量名="value"`
    * 这种方式的特点是：输出变量内容时引号里的变量及命令会经过解析后再输出内容，而不是把双引号中的变量名及命令（命令需要反引起来）原样输出。这种方式比较适合于字符串中附带有变量及命令且想将其解析后再输出的变量定义。
* 2.在Shell中定义变量名及为变量内容赋值的要求
  * 变量名一般是由字母、数字、下划线组成的，可以以字母或下划线开头。
  * 变量的内容可以用单引号或双引号用引起来，也可不加引号，但是这三者的含义是不同的。

```bash
  # 采用不同的方式对普通变量进行定义，并一一打印输出
  [root@herhan ~]# a=192.168.1.2
  [root@herhan ~]# b='192.168.1.2'
  [root@herhan ~]# c="192.168.1.2"
  [root@herhan ~]# echo "a=$a"
  a=192.168.1.2
  [root@herhan ~]# echo "b=$b"
  b=192.168.1.2
  [root@herhan ~]# echo "c=${c}"
  c=192.168.1.2
  # `$`变量名表示输出变量，可以用`$c`和`${c}`两种用法
  [root@herhan ~]# a=192.168.1.2-$a
  [root@herhan ~]# b='192.168.1.2-$a'
  [root@herhan ~]# c="192.168.1.2-$a"
  [root@herhan ~]# echo "a=$a"
  a=192.168.1.2-192.168.1.2
  [root@herhan ~]# echo "b=$b"
  b=192.168.1.2-$a
  [root@herhan ~]# echo "c=${c}"
  c=192.168.1.2-192.168.1.2-192.168.1.2
```

* 3.把一个命令的结果作为变量的内容赋值的方法
  * 1）`变量名=`ls` ` #把命令用反引号引起来，不推荐使用这种方法，因为容易和单引号混淆
  * 2）`变量名=$(ls)` #把命令用$()括起来，推荐使用这种方法

```bash
  # 用两种方法把命令的结果赋值给变量
  [root@herhan /]# CMD=`ls`
  [root@herhan /]# CMD1=$(pwd)
  [root@herhan /]# echo $CMD1
  /
```

```bash
  # 按天打包网站的站点目录程序，生成不同的文件名（此为企业实战案例）
  [root@herhan /]# CMD=$(date +%F)  # 将当前日期（格式为yyyy-mm-dd）赋值给CMD变量
  [root@herhan /]# echo $CMD  
  2021-07-04
  [root@herhan /]# echo $(date +%F).tar.gz
  2021-07-04.tar.gz
  [root@herhan /]# echo `date +%F`.tar.gz
  2021-07-04.tar.gz
  [root@herhan ~]# tar zcf etc_$(date +%F).tar.gz /etc # 将时间作为压缩包名打包tar;从成员名中删除开头的“/” tar;
  tar: Removing leading '/'from member names
  [root@herhan ~]# ls     # 打包结果，包名中包含有当前日期
  etc_2021-07-04.tar.gz
  [root@herhan ~]# H=$(uname -n)
  [root@herhan ~]# echo $H
  herhan
  [root@herhan ~]# tar zcf $H.tar.gz /etc/services
  tar: Removing leading '/'from member names
  [root@herhan ~]# ls -l
  total 9724
  -rw-r--r-- 1 root root 9817374 Jul  4 21:59 etc_2021-07-04.tar.gz
  -rw-r--r-- 1 root root  136214 Jul  4 22:08 herhan.tar.gz
```

* 3.2 局部（普通）变量定义及赋值的经验小结
  * 常规普通变量定义
    * 若变量内容为连续的数字或字符串，赋值时，变量内容两边可以不加引号；
    * 变量的内容很多时，如果有空格且希望解析内容中的变量，就加双引号；
    * 希望原样输出变量中的内容时就用单引号引起内容进行赋值；
  * 希望变量的内容是命令的解析结果的定义及赋值：
    * 要使用反引号将赋值的命令括起来
  * 变量的输出方法如下：
    * 使用“$变量名”即可输出变量的内容，常用“echo $变量名”的方式，也可用printf代替echo输出更复杂的格式内容
  * 变量定义的技巧及注意事项：
    * 注意命令变量内容前后的字符``；
    * 在变量名前加`$`可以取得该变量的值，使用echo或printf命令可以显示变量的值，`$A`和`${A}`的写法不同，但效果是一样的；
    * 用echo等命令输出变量的时候，也可以单引号、双引号、反引号；
    * $dbname_tname,当变量后面连接有其他字符的时候，必须给变量加上大括号{}；

###  3.2. <a name='-1'></a>3.2 变量定义及变量输出说明

名称|解释
-|-
单引号|所见即所得，即输出时会将单引号内的所有内容都原样输出，或者描述为单引号里面看到的是什么就会输出什么，这称为强引用
双引号（默认）|输出双引号内的所有内容；如果内容中有命令（要反引下）、变量、特殊转义符等，会先把变量、命令、转义字符解析出结构，然后再输出最终内容，推荐使用，这称为弱引用
无引号|赋值时，如果变量内容中有空格，则会造成赋值不完整。而在输出内容时，会将含有空格的字符串视为一个整体来输出；如果内容中有命令（要反引下）、变量等，则会先把变量、命令解析出结果，然后输出最终内容；如果字符串中带有空格等特殊字符，则有可能无法完整地输出，因此需要改加双引号。一般连续的字符串、数字、路径等可以不加任何引号进行赋值和输出，不过最好是用双引号替代无引号的情况，特别是对变量赋值时
反引号|``一般用于引用命令，执行的时候命令会被执行，相当于$(),赋值和输出都要用将命令引起来

* 建议
  * 在脚本中定义普通字符串变量时，应尽量把变量的内容用双引号括起来。
  * 单纯数字的变量内容可以不加引号。
  * 希望变量的内容原样输出时需要加单引号。
  * 希望变量值引用命令并获取命令的结果时就用反引号或$()

```bash
  # 对由反引号引起来的`date`命令或$(data)进行测试
  [root@herhan ~]# echo 'today is date'
  today is date
  [root@herhan ~]# echo 'today is `date`'
  today is `date`
  [root@herhan ~]# echo "today is `date`"
  today is Mon Jul  5 12:49:13 CST 2021
  [root@herhan ~]# echo "today is $(date)"
  today is Mon Jul  5 12:49:32 CST 2021
  [root@herhan ~]# echo today is $(date)
  today is Mon Jul 5 12:49:41 CST 2021

  # 变量定义后，在调用变量输出打印时加引号测试
  [root@herhan ~]# echo $HERHAN
  123
  [root@herhan ~]# echo '$HERHAN'
  $HERHAN
  [root@herhan ~]# echo "$HERHAN"
  123

  # 使用三剑客命令中的grep过滤字符串时给过滤的内容加引号
  [root@herhan ~]# echo 123 >grep.log
  [root@herhan ~]# cat grep.log
  123
  [root@herhan ~]# grep "$HERHAN" grep.log
  123
  [root@herhan ~]# grep '$HERHAN' grep.log

  # 使用awk调用Shell中的变量，分别针对加引号、不加引号等情况进行测试
  [root@herhan ~]# ETT=123
  [root@herhan ~]# awk 'BEGIN {print "$ETT"}'
  $ETT
  [root@herhan ~]# awk 'BEGIN {print $ETT}'

  [root@herhan ~]# awk 'BEGIN {print '$ETT'}'
  123 
  [root@herhan ~]# awk 'BEGIN {print "'$ETT'"}'
  123

  [root@herhan ~]# ETT='oldgirl'
  [root@herhan ~]# awk 'BEGIN {print "$ETT"}'
  $ETT
  [root@herhan ~]# awk 'BEGIN {print $ETT}'

  [root@herhan ~]# awk 'BEGIN {print '$ETT'}'

  [root@herhan ~]# awk 'BEGIN {print "'$ETT'"}'
  oldgirl

  [root@herhan ~]# ETT="tingting"
  [root@herhan ~]# awk 'BEGIN {print "$ETT"}'
  $ETT
  [root@herhan ~]# awk 'BEGIN {print $ETT}'

  [root@herhan ~]# awk 'BEGIN {print '$ETT'}'

  [root@herhan ~]# awk 'BEGIN {print "'$ETT'"}'
  tingting
  
  # 结论：不管变量如何定义、赋值，除了加单引号以外，利用awk直接获取变量的输出，结果都是一样的，因此，在awk取用Shell变量时，我们更多地还是喜欢先用echo加符号输出变量，然后通过管道给awk，进而控制变量的输出结果。
  [root@herhan ~]# ETT="oldgirl"
  [root@herhan ~]# echo "$ETT"|awk '{print $0}'
  oldgirl
  [root@herhan ~]# echo '$ETT'|awk '{print $0}'
  $ETT
  [root@herhan ~]# echo $ETT|awk '{print $0}'
  oldgirl
  [root@herhan ~]# ETT=`pwd`
  [root@herhan ~]# echo '$ETT'|awk '{print $0}'
  $ETT
  [root@herhan ~]# echo $ETT|awk '{print $0}'
  /root

  # 通过sed指定变量关键字过滤
  [root@herhan ~]# echo "testchars" > sed.log
  [root@herhan ~]# echo "oldboy" >> sed.log
  [root@herhan ~]# cat sed.log 
  testchars
  oldboy
  [root@herhan ~]# OLDBOY="oldboy"
  [root@herhan ~]# sed -n /"$OLDBOY"/p sed.log
  oldboy
  [root@herhan ~]# sed -n /$OLDBOY/p sed.log
  oldboy
  [root@herhan ~]# sed -n /'$OLDBOY'/p sed.log
```
* 自定义普通字符串变量的建议
  * 1）内容是纯数字、简单的连续字符（内容中不带任何空格）时，定义是可以不加任何引导
  * 2）没有特殊情况时，字符串一律用双引号定义赋值，特别是多个字符串中间有空格时
  * 3）当变量里的内容需要原样输出时，要用单引号（''）,这样的需求极少

##  4. <a name='-1'></a>4.变量定义技巧总结

* 可以多学习和模仿操作系统自带的`/etc/init.d/functions`函数库脚本的定义思路，多学习Linux系统脚本中的定义，有经验的读者最终应形成一套适合自己的规范和习惯
  * 1）变量名及变量内容定义小结
    * 变量名只能为字母、数字或下划线，只能以字母或下划线开头。
    * 变量名的定义要有一定的规范，并且要见名知意。
    * 一般的变量定义、赋值常用双引号；简单连续的字符串可以不加引号；希望原样输出使用单引号
    * 希望变量的内容是命令的解析结果时，要用反引号``,或者用$()命令括起来再赋值
  * 2）Shell定义变量时使用“=”的知识
    * “a=1”里等号是赋值的意思；比较变量是否相等时也可以用“=”或“==”。
  * 3）打印输出及使用变量的知识
    * 打印输出或使用变量时，变量名前要接$符号；变量名后面紧接其他字符的时候，要用大括号将变量部分单独括起来
    * 打印输出或使用变量时，一般加双引号或不加引号；如果是字符串变量，最好加双引号；希望原样输出时使用单引号。

##  5. <a name='Shell-1'></a>5.Shell中特殊且重要的变量

###  5.1. <a name='shell'></a>5.1 shell中特殊位置参数变量

* 常用的特殊位置参数变量说明
位置变量|作用说明
-|-
`$0`|获取当前执行的Shell脚本的文件名，如果执行脚本包含了路径，那么就包括脚本路径
`$n`|获取当前执行的Shell脚本的第n个参数值，n=1...9,当n为0时，表示脚本的文件名；如果n大于9，则用大括号括起来，例如${10},接的参数以空格隔开
`$#`|获取当前执行的Shell脚本后面接的参数的总个数
`$*`|获取当前Shell脚本所有传参的参数，不加引号和`$@`相同；如果给`$*`加上双引号，例如："$*",则表示将所有的参数视为单个字符串，相当于"$1 $2 $3"
`$@`|获取当前Shell脚本所有传参的参数，不加引号和`$*`相同；如果给`$@`加上双引号，例如："$@",则表示将所有的参数视为不同的独立字符串，相当于"$1""$2""$3""...".这是将多参数传递给其他程序的最佳方式，因为它会保留所有的内嵌在每个参数里的任何空白。

```bash
  # $1$2...$9${10}${11}..特殊变量实践
  # 范例：测试$n(n为1..15)的实践
  [root@herhan ~]# echo 'echo $1' > p.sh
  [root@herhan ~]# cat p.sh 
  echo $1
  [root@herhan ~]# sh p.sh herhan
  herhan
  [root@herhan ~]# sh p.sh herhan tim
  herhan
  [root@herhan ~]# sh p.sh "herhan tim"
  herhan tim

  # 范例：在脚本中同时加入$1和$2
  [root@herhan ~]# echo 'echo $1 $2' > p.sh
  [root@herhan ~]# cat p.sh 
  echo $1 $2
  [root@herhan ~]# sh p.sh herhan tim
  herhan tim
  [root@herhan ~]# sh p.sh "herhan tim" john
  herhan tim john

  # 范例：设置15个位置参数（$1...$15）,用于接收命令行传递的15个参数
  [root@herhan ~]# echo \${1..15}
  $1 $2 $3 $4 $5 $6 $7 $8 $9 $10 $11 $12 $13 $14 $15
  [root@herhan ~]# echo "echo \${1..15}"
  echo ${1..15}
  [root@herhan ~]# echo  \${1..15} > n.sh
  [root@herhan ~]# cat n.sh 
  $1 $2 $3 $4 $5 $6 $7 $8 $9 $10 $11 $12 $13 $14 $15
  [root@herhan ~]# vim n.sh 
  [root@herhan ~]# echo {a..z}
  a b c d e f g h i j k l m n o p q r s t u v w x y z
  [root@herhan ~]# sh n.sh {a..z}
  1 b c d e f g h i a0 a1 a2 a3 a4 a5
  [root@herhan ~]# sh n.sh {a..z}
  1 b c d e f g h i a0 a1 a2 a3 a4 a5
  [root@herhan ~]# vim n.sh 
  [root@herhan ~]# sh n.sh {a..z}
  a b c d e f g h i j k l m n o

  
  # $0特殊变量的作用及变量实践
  # 范例：获取脚本的名称及路径
  [root@herhan ~]# cat n.sh
  echo $0
  [root@herhan ~]# sh n.sh
  n.sh
  [root@herhan ~]# sh /root/n.sh 
  /root/n.sh

  # 范例：dirname及basename命令自身的功能和用法
  [root@herhan ~]# dirname /root/n.sh   # dirname命令的作用是获取脚本的路径
  /root
  [root@herhan ~]# basename /root/n.sh  # basename命令的作用是获取脚本的名字 
  n.sh

  # 利用$0和上述命令（dirname，basename）分别取出脚本名称和脚本路径
  [root@herhan ~]# cat n.sh
  dirname $0
  basename $0
  [root@herhan ~]# sh /root/n.sh
  /root
  n.sh

  # $#特殊变量获取脚本传参个数的实践
  # 范例：通过$#获取脚本传参的个数
  [root@herhan ~]# echo \${1..9} > q.sh
  [root@herhan ~]# vim q.sh 
  [root@herhan ~]# cat q.sh
  echo $1 $2 $3 $4 $5 $6 $7 $8 $9
  [root@herhan ~]# echo 'echo $#0' >> q.sh
  [root@herhan ~]# cat q.sh
  echo $1 $2 $3 $4 $5 $6 $7 $8 $9
  echo $#0
  [root@herhan ~]# echo 'echo $#' >> q.sh
  [root@herhan ~]# vim q.sh 
  [root@herhan ~]# sh q.sh {a..z}
  a b c d e f g h i
  26

  # 范例：根据用户再命令行的传参个数判断用户的输入，不合要求的给予提示并退出
  [root@herhan ~]# cat t1.sh
  [ $# -ne 2 ] && {
          echo "muse two args"
          exit 1
  }
  echo herhan
  [root@herhan ~]# sh t1.sh
  muse two args
  [root@herhan ~]# sh t1.sh herhan
  muse two args
  [root@herhan ~]# sh t1.sh herhan tom
  herhan

  
  [root@herhan ~]# cat t2.sh
  if [ $# -ne 2 ];  then
          echo "USAGE:/bin/sh $0 arg1 arg2"
          exit 1
  fi
  echo $1 $2
  [root@herhan ~]# vim t2.sh 
  [root@herhan ~]# sh t2.sh
  USAGE:/bin/sh t2.sh arg1 arg2
  [root@herhan ~]# sh t2.sh herhan tom
  herhan tom

  # $*和$@特殊变量功能及区别说明
  # 范例：利用set设置位置参数
  [root@herhan ~]# set -- "I am" handsome oldboy. # “--”表示清除所有的参数变量，重新设置后面的参数变量
  [root@herhan ~]# echo $#
  3
  [root@herhan ~]# echo $1
  I am
  [root@herhan ~]# echo $2
  handsome
  [root@herhan ~]# echo $3
  oldboy.
  # 测试$*和$@,此时不带双引号
  [root@herhan ~]# echo $*
  I am handsome oldboy.
  [root@herhan ~]# echo $@
  I am handsome oldboy.
  [root@herhan ~]# for i in $*;do echo $i;done
  I
  am
  handsome
  oldboy.
  [root@herhan ~]# for i in $@;do echo $i;done
  I
  am
  handsome
  oldboy.
  # 测试"$*"和"$@",此时带有双引号
  [root@herhan ~]# echo "$*"
  I am handsome oldboy.
  [root@herhan ~]# echo "$@"
  I am handsome oldboy.
  [root@herhan ~]# for i in "$*";do echo $i;done
  I am handsome oldboy.
  [root@herhan ~]# for i in "$@";do echo $i;done
  I am
  handsome
  oldboy.
  [root@herhan ~]# for i;do echo $i;done
  I am
  handsome
  oldboy.
  [root@herhan ~]# for i in $*;do echo $i;done
  I
  am
  handsome
  oldboy.
  [root@herhan ~]# shift
  [root@herhan ~]# echo $#
  2
  [root@herhan ~]# echo $1
  handsome
  [root@herhan ~]# echo $2
  oldboy.
```

###  5.2. <a name='Shell-1'></a>5.2 Shell进程中的特殊状态变量
* Shell进程的特殊状态变量
位置变量|作用说明
-|-
`$?`|获取执行上一指令的执行状态返回值（0为成功，非零为失败），这个变量最常用
`$$`|获取当前执行的Shell脚本的进程号（PID），这个变量不常用，了解即可
`$!`|获取上一个在后台工作的进程的进程号（PID），这个变量不常用，了解即可
`$_`|获取在此之前执行的命令或脚本的最后一个参数，这个变量不常用，了解即可

* 在企业场景下，“$?”返回值的用法如下：
  * （1）判断命令、脚本或函数等程序是否执行成功
  * （2）若在脚本执行“exit 数字”，则会返回这数字个“$?”变量
  * （3）如果是在函数里，则通过“return 数字”把这个数字以函数返回值的形式传给“$?”.
```bash
  # $? 特殊变量功能实践

  ## 执行命令后获取返回值
  [root@herhan ~]# pwd
  /root
  [root@herhan ~]# echo $?
  0
  [root@herhan ~]# ls /home/hh
  ls: cannot access /home/hh: No such file or directory # 提示文件夹不存在
  [root@herhan ~]# echo $?
  2
  [root@herhan ~]# rm -rf /root
  rm: cannot remove ‘/root’: Device or resource busy # 提示文件被占用
  [root@herhan ~]# echo $?
  1
  [root@herhan ~]# kkk
  -bash: kkk: command not found   #命令不存在
  [root@herhan ~]# echo $?
  127

  ## 根据返回值来判断软件的安装步奏是否成功
  [root@herhan ~]# wget -c -q http://nginx.org/download/nginx-1.10.1.tar.gz
  [root@herhan ~]# ls -l
  total 1520
  -rw-r--r-- 1 root root 909077 Jun  1  2016 nginx-1.10.1.tar.gz
  -rw-r--r-- 1 root root 644061 Jul 10 23:53 nginx-1.10.1.tar.gz.1
  [root@herhan ~]# rm nginx-1.10.1.tar.gz.1 
  rm: remove regular file ‘nginx-1.10.1.tar.gz.1’? y
  [root@herhan ~]# tar xf nginx-1.10.1.tar.gz 
  [root@herhan ~]# cd nginx-1.10.1
  [root@herhan nginx-1.10.1]# ./configure --prefix=/usr/local/myWorkSpace/nginx1.16 --with-http_stub_status_module --with-http_ssl_module --with-debug
  [root@herhan nginx-1.10.1]# echo $?
  0
  [root@herhan nginx-1.10.1]# make && make install
  [root@herhan nginx-1.10.1]# echo $?
  0

  ## 通过获取$?的返回值群定网站备份是否正确
  [root@herhan ~]# cd /etc/
  [root@herhan etc]# tar zcf /opt/services.tar.gz ./services 
  [root@herhan etc]# echo $?

  ## 通过脚本控制命令及脚本执行后的返回值
    [root@herhan ~]# cat test4.sh 
    [ $# -ne 2 ] && {
            echo "must be two args."
            exit 119
    }
    echo herhan
  [root@herhan ~]# sh test4.sh 
  must be two args.
  [root@herhan ~]# echo $?
  119
  [root@herhan ~]# sh test4.sh a1 a2
  herhan
  [root@herhan ~]# echo $?
  0



  # $$特殊比变量工能及实践
  ## 获取脚本执行的进程号（PID）
  [root@herhan ~]# cat test_pid.sh 
  echo $$ >/tmp/a.pid
  sleep 300
  [root@herhan ~]# ps -ef|grep test_pid|grep -v grep
  [root@herhan ~]# sh test_pid.sh &
  [1] 17511
  [root@herhan ~]# ps -ef|grep test_pid|grep -v grep
  root     17511 17470  0 13:06 pts/0    00:00:00 sh test_pid.sh
  [root@herhan ~]# cat /tmp/a.pid 
  17511

  ## 实现系统中多次执行某一个脚本后的进程只有一个（此为$$的企业级应用）
  ### 说明：有时执行定时任务脚本的频率比较快，并不知道上一个脚本是否真的执行完毕，但是，业务要求同一时刻只能有一个同样的脚本在运行，此时就可以利用$$获取上一次运行的脚本进程号，当程序重新运行时，根据获得的进程号，清理掉上一次的进程，运行新的脚本命令
  [root@herhan ~]# cat pid.sh 
  # !/bin/sh
  pidpath=/tmp/a.pid
  if [ -f "$pidpath"] then
          kill `cat $pidpath` > /dev/null 2>&1
          rm -f $pidpath
  fi
  echo $$ >$pidpath
  sleep 300
  [root@herhan ~]# vim pid.sh 
  [root@herhan ~]# ps -ef|grep pid.sh|grep -v grep
  [root@herhan ~]# sh pid.sh &
  [1] 24248
  [root@herhan ~]# ps -ef|grep pid.sh|grep -v grep
  root     24248 24187  0 18:01 pts/0    00:00:00 sh pid.sh
  [root@herhan ~]# sh pid.sh &
  [2] 24257
  [root@herhan ~]# ps -ef|grep pid.sh|grep -v grep
  root     24257 24187  0 18:01 pts/0    00:00:00 sh pid.sh
  [1]-  Terminated              sh pid.sh
  [root@herhan ~]# ps -ef|grep pid.sh|grep -v grep
  root     24257 24187  0 18:01 pts/0    00:00:00 sh pid.sh
  [root@herhan ~]# sh pid.sh &
  [3] 24273
  [root@herhan ~]# ps -ef|grep pid.sh|grep -v grep
  root     24273 24187  0 18:04 pts/0    00:00:00 sh pid.sh
  [2]-  Terminated              sh pid.sh
  [root@herhan ~]# ps -ef|grep pid.sh|grep -v grep
  root     24273 24187  0 18:04 pts/0    00:00:00 sh pid.sh


  # $_特殊变量功能说明及实践
  ## $_参数的示例
  [root@herhan ~]# cat 123 321
  cat: 123: No such file or directory
  cat: 321: No such file or directory
  [3]+  Done                    sh pid.sh
  [root@herhan ~]# echo $_
  321


  # $!特殊变量功能说明及实践
  ## $!的功能示例
  [root@herhan ~]# ps -ef|grep pid.sh|grep -v grep
  [root@herhan ~]# sh pid.sh &
  [1] 24295
  [root@herhan ~]# echo $!
  24295
  [root@herhan ~]# ps -ef|grep pid.sh|grep -v grep
  root     24295 24187  0 18:18 pts/0    00:00:00 sh pid.sh
```

##  6. <a name='bashShell'></a>6.bash Shell内置变量命令
* bash Shell包含一些内置命令。这些内置命令在目录列表里是看不见的，它们由Shell本身提供。
* 常用的内部命令有：`echo、eval、exec、export、read、shift`
###  6.1. <a name='echo'></a>6.1 echo 在屏幕上输出信息
* 命令格式：`echo args#<==可以是字符串和变量的组合`。
* 功能说明：将echo命令后面args指定的字符串及变量等显示到标准输出。
* 常用参数

echo 参数选项|说明
-|-
`-n`|不换行输出内容
`-e`|解析转义字符（见下面的字符）
转义字符：|
`\n`|换行
`\r`|回车
`\t`|制表符（tab）
`\b`|退格
`\v`|纵向制表符

```bash
  # echo的参数应用实例
  [root@herhan ~]# echo herhan;echo john
  herhan
  john
  [root@herhan ~]# echo -n herhan;echo john
  herhanjohn
  [root@herhan ~]# echo "herhan\tjohn\nherhan\tjohn"
  herhan\tjohn\nherhan\tjohn
  [root@herhan ~]# echo -e "herhan\tjohn\nherhan\tjohn"
  herhan  john
  herhan  john
  [root@herhan ~]# printf "herhan\tjohn\nherhan\tjohn\n"
  herhan  john
  herhan  john
  [root@herhan ~]# echo -e "1\b23"
  23
  [root@herhan ~]# printf "1\b23\n"
  23
```

###  6.2. <a name='eval'></a>6.2 eval 
* 命令格式：eval args
* 功能：当Shell程序执行到eval语句时，Shell读入参数args，并将它们组合成一个新的命令，然后执行

```bash
  [root@herhan etc]# vim noeval.sh
  [root@herhan etc]# cat noeval.sh 
  echo \$$# #<==$#表示传参的个数
  [root@herhan etc]# sh noeval.sh arg1 arg2
  $2
  [root@herhan etc]# vim noeval.sh
  [root@herhan etc]# cat noeval.sh 
  eval "echo \$$#"
  [root@herhan etc]# sh noeval.sh arg1 arg2
  arg2
```

###  6.3. <a name='exec'></a>6.3 exec
* 命令格式：exec命令参数
* 功能：exec命令能够在不创建新的子进程的前提下，转去执行指定的命令，当指定的命令执行完毕后，该进程（也就是最初的Shell）就终止了。
```bash
  [root@herhan etc]# exec date
  Wed Jul 14 20:12:54 CST 2021

  SSH   101.200.156.233: session closed
  Press any key to reconnect
```
* 当使用exec打开文件后，read命令每次都会将文件指针移动到文件的下一行进行读取，直到文件末尾，利用这个可以实现处理文件内容。
```bash
  # exec的功能示例
  [root@herhan ~]# seq 5 > /tmp/tmp.log
  [root@herhan ~]# vim exec.sh
  [root@herhan ~]# cat exec.sh 
  exec < /tmp/tmp.log  #<==读取log内容
  while read line      #<==利用read一行行读取处理
  do 
    echo $line        #<==打印输出 
  done
  echo ok
  [root@herhan ~]# sh exec.sh 
  1
  2
  3
  4
  5
  ok 
```

###  6.4. <a name='read'></a>6.4 read
* 命令格式：read  变量名表
* 功能：从标准输入读取字符串等信息，传给Shell程序内部定义的变量

###  6.5. <a name='shift'></a>6.5 shift
* 命令格式：shift-Shift positional parameters
* 功能：shift语句会按如下方式重新命名所有的位置参数变量，即`$2`成为`$1`、`$3`成为`$2`等，以此类推，在程序中每使用一次shift语句，都会使所有的位置参数依次向左移动一个位置，并使位置参数`$#`减1，直到减到0为止。
```bash
  # shift的功能介绍
  [root@herhan ~]# help shift
  shift: shift [n]
      Shift positional parameters.
      
      Rename the positional parameters $N+1,$N+2 ... to $1,$2 ...  If N is
      not given, it is assumed to be 1.
      
      Exit Status:
      Returns success unless N is negative or greater than $#.
```
* shift命令的主要作用将位置参数`$1`、`$2`等进行左移，即如果位置参数是`$3`、`$2`、`$1`,那么执行一次shift后，`$3`就变成了`$2`,`$2`变成了`$1`,`$1`就是消失了。
```bash
  [root@herhan ~]# vim n.sh
  [root@herhan ~]# cat n.sh
  echo $1 $2
  if [ $# -eq 2 ];then
          shift
          echo $1
  fi
  [root@herhan ~]# sh n.sh 1 2
  1 2
  2
```

* 应用场景：当我们写Shell希望像命令行的命令通过参数控制不同的功能时，就会先传一个类似-c的参数，然后再接内容。
```bash
  # 系统案例：ssh-copy-id -i /root/.ssh/id_dsa.pub:
  ID_FILE="${HOME}/.ssh/id_rsa.pub"
  if [ "-i" = "$1" ];then
    shift
    # check if we have 2 parameters left,if so the first is the new ID file
    if [ -n "$2" ];then
      if expr "$1" : ".*\.pub" > /dev/null ; then
        ID_FILE="$1"
      else
        ID_FILE="$1.pub"
      fi
      shift   # and this should leave $1 as the target name
    fi
  fi
```

###  6.6. <a name='exit'></a>6.5 exit
* 命令格式：exit-Exit the shell
* 功能：退出Shell程序。在exit之后可以有选择地指定一个数位作为返回状态

##  7. <a name='Shell-1'></a>7.Shell变量子串知识及实践
###  7.1. <a name='Shell-1'></a>7.1 Shell变量子串介绍

ID|表达式|说明
-|-|-
1|`${parameter}`|返回变量$parameter的内容
2|`${#parameter}`|返回变量$parameter内容的长度（按字符），也适用于特殊变量
3|`${parameter:offset}`|在变量${parameter}中，从位置offset之后开始提取子串到结尾
4|`${parameter:offset:length}`|在变量${parameter}中，从位置offset之后开始提取长度为length的子串
5|`${parameter#word}`|从变量${parameter}开头开始删除最短区配的word子串
6|`${parameter##word}`|从变量${parameter}开头开始删除最长匹配的word子串
7|`${parameter%word}`|从变量${parameter}结尾开始删除最短匹配的word子串
8|`${parameter%%word}`|从变量${parameter}结尾开始删除最长匹配的word子串
9|`${parameter/pattern/string}`|使用string代替第一个匹配的pattern
10|`${parameter/pattern/string}`|使用string代替所有匹配的pattern

###  7.2. <a name='Shell-1'></a>7.2 Shell变量子串的实践
```bash
  # 注备：定义HERHAN变量，赋值内容"I am herhan",操作代码如下：
  [root@herhan ~]# HERHAN="I am herhan"
  [root@herhan ~]# echo ${HERHAN}
  I am herhan
  [root@herhan ~]# echo $HERHAN
  I am herhan

  # 返回HERHAN变量值的长度
  ## 通过在变量名前加#，就可以打印变量值的长度：
  [root@herhan ~]# echo ${#HERHAN}
  11
  ## Shell的其他打印量长度的方法
  [root@herhan ~]# echo $HERHAN|wc -L     #<==输出变量值，然后通过管道交给wc计算长度。
  11
  [root@herhan ~]# expr length "$HERHAN"  #<==利用expr的length函数计算变量长度
  11
  [root@herhan ~]# echo "$HERHAN"|awk '{print length($0)}'  #<==利用awk的length函数计算变量长度，也可无"$0"这几个字符
  11

  # 利用time命令及for循环对几种获取字符串长度的方法进行性能比较
  ## (1)变量自带的获取长度的方法（echo ${#char}）
   [root@herhan ~]# time for n in {1..10000};do char=`seq -s "herhan" 100`;echo ${#char} &>/dev/null;done

  real    0m10.671s
  user    0m4.555s
  sys     0m5.412s
  ## (2)利用管道加wc的方法（echo ${char}|wc -L）
  [root@herhan ~]# time for n in {1..10000};do char=`seq -s "herhan" 100`;echo ${char}|wc -L &>/dev/null;done

  real    0m41.681s
  user    0m16.461s
  sys     0m22.732s
  ## (3)利用expr自带的length方法（expr length"${char}"）
  [root@herhan ~]# time for n in {1..10000};do char=`seq -s "herhan" 100`;expr length "${char}" &>/dev/null;done

  real    0m20.947s
  user    0m9.522s
  sys     0m9.957s
  ## (4)利用awk自带的length函数方法
  [root@herhan ~]# time for n in {1..10000};do char=`seq -s "herhan" 100`;echo $char|awk '{print length($0)}' &>/dev/null;done

  real    0m34.515s
  user    0m13.990s
  sys     0m18.146s
```

* 可以看到，这几种方法的速度相差几十到上百倍，一般情况下调用外部命令来处理的方式与使用内置操作的速度相差较大。在Shell编程中，应尽量使用内置操做或函数来完成。
* 有关获取字符串长度的几种统计方法的性能比较如下：
  * 变量自带的计算长度的方法的效率最高，在要求效率的场景中尽量多用。
  * 使用管道统计的方法的效率都比较差，在要求效率的场景中尽量不用。
  * 对于日常简单的脚本计算，读者可以根据自己所擅长的或易用的程度区选择

```bash
  # 关于计算字符串的长度，有一个企业面试案例，面试题目如下：
  # 请编写Shell脚本以打印下面语句中字符数小于6的单词
  # I am herhan linux，welcome to our training.
  #! /bin/bash
  a=1
  while [ $a -le $# ]
  do
          char=$(eval "echo \$$a")
          # echo $char    
          if [ ${#char} -lt 6 ];then
                  echo $char
          fi
          let a++
  done
```

```bash
  # 截取HERHAN变量的内容，从第2个字符之后开始截取，默认截取后面字符的全部。第2个字符不包含在内，也可以理解为删除前面的多个字符
  [root@herhan ~]# echo ${HERHAN}
  I am herhan
  [root@herhan ~]# echo ${HERHAN:2}
  am herhan
  # 截取HERHAN变量的内容，从第2个字符之后开始截取，截取2个字符
  [root@herhan ~]# echo ${HERHAN:2:2}
  am
  [root@herhan ~]# echo ${HERHAN}|cut -c 3-4
  am
  # 从变量$HERHAN内容的开头开始删除最短匹配“a*C”及“a*c”的子串
  [root@herhan ~]# HERHAN=abcABC123ABCabc
  [root@herhan ~]# echo $HERHAN
  abcABC123ABCabc
  [root@herhan ~]# echo ${HERHAN#a*C}
  123ABCabc
  [root@herhan ~]# echo ${HERHAN#a*c}
  ABC123ABCabc
  # 从变量$HERHAN内容的开头开始删除最长匹配“a*C”及“a*c”的子串
  [root@herhan ~]# echo ${HERHAN##a*C}
  abc
  # 从变量$HERHAN内容的尾部开始删除最短匹配“a*C”及“a*c”的子串
  [root@herhan ~]# echo ${HERHAN%a*C}
  abcABC123ABCabc
  [root@herhan ~]# echo ${HERHAN%a*c}
  abcABC123ABC
  # 从变量$HERHAN内容的尾部开始删除最长匹配“a*C”及“a*c”的子串
  [root@herhan ~]# echo ${HERHAN%%a*C}
  abcABC123ABCabc
  [root@herhan ~]# echo ${HERHAN%%a*c}

```
* 有关上述匹配删除的小结：
  * `#`表示从开头删除匹配最短
  * `##`表示从开头删除匹配最长
  * `%`表示从结尾删除匹配最短
  * `%%`表示从结尾删除匹配最长
  * `a*c`表示匹配的字符串，`*`表示匹配所有，`a*c`匹配开头为`a`、中间为任意多个字符、结尾为`c`的字符串
  * `a*C`表示匹配的字符串，`*`表示匹配所有，`a*C`匹配开头为`a`、中间为任意多个字符、结尾为`C`的字符串

```bash
  # 使用herhan字符串代替变量$HERHAN匹配的john字符串
  [root@herhan ~]# HERHAN="I am john,yes,john"
  [root@herhan ~]# echo $HERHAN
  I am john,yes,john
  [root@herhan ~]# echo ${HERHAN/john/herhan}
  I am herhan,yes,john
  [root@herhan ~]# echo ${HERHAN//john/herhan}
  I am herhan,yes,herhan
```

* 有关替换的小结：
  * 一个`/`表示替换匹配的第一个字符串
  * 两个`/`表示替换匹配的所有字符串

###  7.3. <a name='-1'></a>7.3 变量子串的生产场景应用案例

```bash
  # 去掉下面所有文件的文件名中断的"_finished"字符串
  -rw-r--r-- 1 root root      0 Jul 16 17:18 stu_102999_1_finished.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:18 stu_102999_2_finished.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:18 stu_102999_3_finished.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:18 stu_102999_4_finished.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:18 stu_102999_5_finished.jpg
  
  ## 方法1：shell脚本for循环结合sed实现
  [root@herhan ~]# cat herhan_modi_file.sh 
  #! /bin/bash
  for file in `ls ./*.jpg`
  do 
          mv $file `echo $file|sed 's/_finished//g'`
  done
  [root@herhan ~]# ls -l
  -rw-r--r-- 1 root root      0 Jul 16 17:18 stu_102999_1.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:18 stu_102999_2.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:18 stu_102999_3.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:18 stu_102999_4.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:18 stu_102999_5.jpg

  ## 方法2：shell脚本for循环加变量的部分截取方法
  [root@herhan ~]# cat herhan_change_file.sh 
  #! /bin/bash
  for file in `ls ./*.jpg`
  do
          mv $file `echo "${file%_finished*}.jpg"`
  done
  [root@herhan ~]# ls -l
  -rw-r--r-- 1 root root      0 Jul 16 17:18 stu_102999_1.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:18 stu_102999_2.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:18 stu_102999_3.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:18 stu_102999_4.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:18 stu_102999_5.jpg

  ## 方法3：ls结合awk实现
  [root@herhan ~]# ls | awk -F '_finished' '{print $0}'
  stu_102999_1_finished.jpg
  stu_102999_2_finished.jpg
  stu_102999_3_finished.jpg
  stu_102999_4_finished.jpg
  stu_102999_5_finished.jpg
  [root@herhan ~]# ls | awk -F '_finished' '{print $1}'
  stu_102999_1
  stu_102999_2
  stu_102999_3
  stu_102999_4
  stu_102999_5
  [root@herhan ~]# ls | awk -F '_finished' '{print $2}'
  .jpg
  .jpg
  .jpg
  .jpg
  .jpg
  [root@herhan ~]# ls | awk -F '_finished' '{print $1$2}'
  stu_102999_1.jpg
  stu_102999_2.jpg
  stu_102999_3.jpg
  stu_102999_4.jpg
  stu_102999_5.jpg
  [root@herhan ~]# ls | awk -F '_finished' '{print "mv "$0" "$1$2" "}'|/bin/bash
  [root@herhan ~]# ls -l
  -rw-r--r-- 1 root root      0 Jul 16 17:33 stu_102999_1.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:33 stu_102999_2.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:33 stu_102999_3.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:33 stu_102999_4.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:33 stu_102999_5.jpg
  
  ##  方法4：通过专业的改名命令rename实现
  [root@herhan ~]# rename "_finished" "" *
  [root@herhan ~]# ls -l
  -rw-r--r-- 1 root root      0 Jul 16 17:33 stu_102999_1.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:33 stu_102999_2.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:33 stu_102999_3.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:33 stu_102999_4.jpg
  -rw-r--r-- 1 root root      0 Jul 16 17:33 stu_102999_5.jpg
  [root@herhan ~]# rename .jpg .herhan *
  -rw-r--r-- 1 root root      0 Jul 16 17:41 stu_102999_1.herhan
  -rw-r--r-- 1 root root      0 Jul 16 17:41 stu_102999_2.herhan
  -rw-r--r-- 1 root root      0 Jul 16 17:41 stu_102999_3.herhan
  -rw-r--r-- 1 root root      0 Jul 16 17:41 stu_102999_4.herhan
  -rw-r--r-- 1 root root      0 Jul 16 17:41 stu_102999_5.herhan
```

##  8. <a name='Shell-1'></a>8.Shell特殊扩展变量的知识与实践
###  8.1. <a name='shell-1'></a>8.1 shell特殊扩展变量介绍

表达式|说明
-|-
`${parameter:-word}`|如果parameter的变量值为空或未赋值，则会返回word字符串并替代变量的值；用途：如果变量未定义，则返回备用的值，防止变量未空值或因未定义而导致异常。
`${parameter:=word}`|如果parameter的变量值为空或未赋值，则设置这个变量值为word，并返回其值；用途：基本同上一个${parameter:-word},但该变量又额外给parameter变量赋值了。
`${parameter:?word}`|如果parameter变量值为空或未赋值，那么word字符串将被作为标准错误输出，否则输出变量的值；用途用于捕捉由于变量未定义导致的错误，并退出程序
`${parameter:+word}`|如果parameter变量值为空或未赋值，则什么都不做，否则word字符串将替代变量的值

###  8.2. <a name='shell-1'></a>8.2 shell特殊扩展变量的实践

```bash
  # 范例：${parameter:-word}用法功能示例
  [root@backup ~]# echo $test

  [root@backup ~]# result=${test:-UNSET}
  [root@backup ~]# echo $result 
  UNSET
  [root@backup ~]# echo ${test}  # 注意，此时打印test变量还是为空
  [root@backup ~]# test=herhan
  [root@backup ~]# result=${test:-UNSET}
  [root@backup ~]# echo $result 
  herhan

  # 范例：${parameter:=word}用法功能示例
  [root@backup ~]# unset result 
  [root@backup ~]# echo $result 

  [root@backup ~]# unset test 
  [root@backup ~]# echo $test

  [root@backup ~]# result=${test:=UNSET}
  [root@backup ~]# echo $result 
  UNSET
  [root@backup ~]# echo $test
  UNSET

  # 范例：${parameter:?word}功能实践
  [root@backup ~]# echo ${key?not defined}
  -bash: key: not defined
  [root@backup ~]# echo ${key:?not defined}
  -bash: key: not defined
  [root@backup ~]# key=1
  [root@backup ~]# echo ${key:?not defined}
  1
  [root@backup ~]# echo ${key?not defined}
  1

  # 范例：${parameter:+word}功能实践
  [root@backup ~]# herhan=${john:+word}
  [root@backup ~]# echo $herhan

  [root@backup ~]# john=19
  [root@backup ~]# herhan=${john:+word}
  [root@backup ~]# echo $herhan
  word
```
###  8.3. <a name='Shell-1'></a>8.3 Shell特殊扩展变量的生产场景应用案例

```bash
  # 范例：删除7天前的过期数据备份
  [root@backup ~]£ cat del.sh
  find ${path-/tmp} -name "*.tar.gz" -type f -mtime +7|xargs rm -f
  [root@backup ~]£ sh -x del.sh 
  + xargs rm -f
  + find /tmp -name '*.tar.gz' -type f -mtime +7
```

##  9. <a name='-1'></a>9.变量的数值计算实践
###  9.1. <a name='-1'></a>9.1 算术运算符
* Shell中常见的算术运算符号

算术运算符|意义（*表示常用）
-|-
`+、-`|加法（或正号）、减法（或负号）*
`*、/、%`|乘法、除法、取余（取模）*
`**`|幂运算*
`++、--`|增加及减少，可前置也可放在变量结尾*
`!、&&、||`|逻辑非（取反）、逻辑与（and）、逻辑或（or）*
`<、<=、>、>=`|比较符号（小于、小于等于、大于、大于等于）
`==、!=、=`|比较符号（相等、不相等、对于字符串“=”也可以表示相当于）*
`<<、>>`|向左移位、向右移位
`~、|、&、^`|按位取反、按位异或、按位与、按位或
`=、+=、-=、*=、/=、%=`|赋值运算符，例如a+=1相当于a=a+1,a-=1相当于a=a-1*

* Shell中常见的算术运算命令

运算操作符与运算命令|意义
-|-
`(())`|用于整数运算的常用运算符，效率很高
`let`|用于整数运算，类似于"(())"
`expr`|可用于整数运算，但还是很多其他的额外功能
`bc`|Linux下的一个计算器程序（适合整数及小数运算）
`$[]`|用于整数运算
`awk`|awk既可以用于整数运算，也可以用于小数运算
`declare`|定义变量值和属性，-i参数可以用于定义整形变量，做运算

###  9.2. <a name='-1'></a>9.2 双小括号"(())"数值运算命令
* 双小括号"(())"数值运算的基础语法

运算操作符与运算命令|意义
-|-
`((i=i+1))`|此种书写方法为运算后负值法，即将i+1的运算结果赋值给变量i。注意，不用"echo((i+1))"的形式输出表达式的值，但可以用echo$((i=i+1))输出其值
`i=$((i+1))`|可以在"(())"前加$符，表示将表达式运算后赋值给i
`((8>7&&5==5))`|可以进行比较操作，还可以加入逻辑与和逻辑或，用于条件判断
`echo $((2+1))`|需要直接输出运算表达式的运算结果时，可以在"(())"前加$符



* 范例
```bash
  # 范例：利用"(())"进行简单的数值计算
  [root@backup ~]# echo $((1+1))
  2
  [root@backup ~]# echo $((6-3))
  3
  [root@backup ~]# ((i=5))
  [root@backup ~]# ((i=i*2))
  [root@backup ~]# echo $i
  10

  # 范例：利用"(())"进行稍微复杂一些的综合算术运算
  [root@backup ~]# ((a=1+2**3-4%3))
  [root@backup ~]# echo $a
  8
  [root@backup ~]# b=$((a=1+2**3-4%3))
  [root@backup ~]# echo $b
  8
  [root@backup ~]# echo $((a=1+2**3-4%3))
  8
  [root@backup ~]# a=$((100*(100+1)/2))
  [root@backup ~]# echo $a
  5050
  [root@backup ~]# echo $((100*(100+1)/2))
  5050

  # 范例：特殊运算符号的运算小示例
  [root@backup ~]# a=8
  [root@backup ~]# echo $((a=a+1))
  9
  [root@backup ~]# echo $((a+=1))
  10
  [root@backup ~]# echo $((a**2))
  100

  # 范例：利用"(())"双括号进行比较及判断
  [root@backup ~]# echo $((3<8))
  1
  [root@backup ~]# echo $((8<3))
  0
  [root@backup ~]# echo $((8==8))
  1
  [root@backup ~]# if ((8>7&&5==5))
  > then
  > echo yes
  > fi
  yes
  # 提示：上面涉及的数字及变量必须为整数（整型），不能是小数（浮点数）或字符串。后面的bc和awk命令可以用于进行小数（浮点数）运算，但一般用到的较少

  # 范例：在变量前后使用--和++特殊运算符的表达式
  [root@backup ~]# a=10
  [root@backup ~]# echo $((a++))
  10
  [root@backup ~]# echo $a
  11
  [root@backup ~]# echo $((a--))
  11
  [root@backup ~]# echo $a
  10
  [root@backup ~]# a=10
  [root@backup ~]# echo $a
  10
  [root@backup ~]# echo $((--a))
  9
  [root@backup ~]# echo $a
  9
  [root@backup ~]# echo $((++a))
  10
  [root@backup ~]# echo $a
  10
  # 提示：有关++、--运算的记忆方法：
  # 变量a在运算符之前，输出表达式的值为a，然后a自增或自减；变量a在运算符之后，输出表达式会先自增或自减，表达式的值就是自增或自减后a的值。

  # 范例：通过"(())"运算后赋值给变量
  [root@backup ~]# myvar=99
  [root@backup ~]# echo $((myvar+1))
  100
  [root@backup ~]# echo $((   myvar + 1  ))
  100
  [root@backup ~]# myvar=$((myvar+1))
  [root@backup ~]# echo $myvar
  100
  # 提示：在"(())"中使用变量时可以去掉变量前的$符号

  # 范例：包含"(())"的各种常见运算符命令行的执行示例
  [root@backup ~]# echo $((6+2))
  8
  [root@backup ~]# echo $((622))
  622
  [root@backup ~]# echo $((6-2))
  4
  [root@backup ~]# echo $((6*2))
  12
  [root@backup ~]# echo $((6/2))
  3
  [root@backup ~]# echo $((6%2))
  0
  [root@backup ~]# echo $((6**2))
  36

  # 范例：各种"(())"运算的Shell脚本示例
  [root@backup ~]# cat test.sh
  a=6
  b=2
  echo "a-b=$(($a-$b))"
  echo "a+b=$(($a+$b))"
  echo "a*b=$(($a*$b))"
  echo "a/b=$(($a/$b))"
  echo "a**b=$(($a**$b))"
  echo "a%b=$(($a%$b))"
  [root@backup ~]# sh test.sh
  a-b=4
  a+b=8
  a*b=12
  a/b=3
  a**b=36
  a%b=0

  # 范例：把上面的a、b两个变量通过命令行脚本传参，以实现混合运算脚本的功能
  [root@backup ~]# cat test.sh 
  a=$1
  b=$2
  echo "a-b=$(($a-$b))"
  echo "a+b=$(($a+$b))"
  echo "a*b=$(($a*$b))"
  echo "a/b=$(($a/$b))"
  echo "a**b=$(($a**$b))"
  echo "a%b=$(($a%$b))"
  [root@backup ~]# sh test.sh 6 3
  a-b=3
  a+b=9
  a*b=18
  a/b=2
  a**b=216
  a%b=0
  [root@backup ~]# sh test.sh 10 6
  a-b=4
  a+b=16
  a*b=60
  a/b=1
  a**b=1000000
  a%b=4

  # 范例：实现输入2个数进行加、减、乘、除功能的计算器
  [root@backup ~]# cat 05_10_jisuan.sh 
  #!/bin/bash
  # add,subtract,multiply and divide 
  print_usage(){
          printf "Please enter an intger\n"
          exit 1
  }
  read -p "Please input first number: " firstnum
  if [ -n "`echo $firstnum|sed 's/[0-9]//g'`" ];then
          print_usage
  fi
  read -p "Please input the operaters: " operators
  if [ "${operators}" != "+" ] && [ "${operators}" != "-" ] && [ "${operators}" != "*" ] && [ "${operators}" != "/" ];then
          echo "please use {+|-|*|/}"
          echo $operators
          exit 2
  fi
  read -p "Please input second number: " secondnum
  if [ -n "`echo $secondnum|sed 's/[0-9]//g'`" ];then
          print_usage
  fi
  echo "${firstnum}${operators}${secondnum}=$((${firstnum}${operators}${secondnum}))"
  [root@backup ~]# vim 05_10_jisuan.sh
  [root@backup ~]# sh 05_10_jisuan.sh 
  Please input first number: 1
  Please input the operaters: +
  Please input second number: 3
  1+3=4
  [root@backup ~]# sh 05_10_jisuan.sh 
  Please input first number: 3
  Please input the operaters: ^
  please use {+|-|*|/}
  ^

  # 方法二
  [root@backup ~]# cat 05_11_jisuan.sh 
  #!/bin/bash
  # add ,subtract,multiply and divide
  print_usage(){
          printf $"USAGE:$0 NUM1 {+|-|*|/} NUM2\n"
          exit 1
  }
  if [ $# -ne 3 ];then
          print_usage
  fi
  firstnum=$1
  secondnum=$3
  op=$2
  if [ -n "`echo $firstnum|sed 's/[0-9]//g'`" ];then
          print_usage
  fi
  if [ "$op" != "+" ]&&[ "$op" != "-" ]&&[ "$op" != "*" ]&&[ "$op" != "/" ];then
          print_usage
  fi
  if [ -n "`echo $secondnum|sed 's/[0-9]//g'`" ];then
          print_usage
  fi
  echo "${firstnum}${op}${secondnum}=$((${firstnum}${op}${secondnum}))"
  [root@backup ~]# sh 05_11_jisuan.sh 
  USAGE:05_11_jisuan.sh NUM1 {+|-|*|/} NUM2
  [root@backup ~]# sh 05_11_jisuan.sh 1+2
  USAGE:05_11_jisuan.sh NUM1 {+|-|*|/} NUM2
  [root@backup ~]# sh 05_11_jisuan.sh 1 + 2
  1+2=3
  [root@backup ~]# sh 05_11_jisuan.sh 1 * 2
  USAGE:05_11_jisuan.sh NUM1 {+|-|*|/} NUM2
  [root@backup ~]# sh 05_11_jisuan.sh 1 \* 2
  1*2=2
  [root@backup ~]# sh 05_11_jisuan.sh 1 / 2
  1/2=0

  # 方法三
  [root@backup ~]# cat jisuan1.sh
  echo $(($1))
  [root@backup ~]# sh jisuan1.sh 
  0
  [root@backup ~]# sh jisuan1.sh 1+1
  2
  [root@backup ~]# sh jisuan1.sh "1 + 1"
  2
```

###  9.3. <a name='let'></a>9.3 let运算命令的用法
* `let`运算命令的语法格式为：`let` 赋值表达式
* `let`赋值表达式的功能等同于"((赋值表达式))"

* 范例
```bash
  # 范例：给自变量i加8
  [root@backup ~]# i=2
  [root@backup ~]# i=i+8
  [root@backup ~]# echo $i
  i+8
  [root@backup ~]# unset i
  [root@backup ~]# i=2
  [root@backup ~]# let i=i+8
  [root@backup ~]# echo $i
  10

  # 范例：监控Web服务状态，如果访问两次均失败
  [root@backup ~]# cat 05_12_checkurl.sh 
  #!/bin/bash
  CheckUrl(){
          timeout=5
          fails=0
          success=0
          while true
          do
                  wget --timeout=$timeout --tries=1 http://www.baidu.com -q -O /dev/null
                  if [ $? -ne 0 ];then
                          let fails=fails+1
                  else
                          let success=success+1
                  fi
                  if [ $success -ge 1 ];then
                          echo success
                          exit 0
                  fi
                  if [ $fails -ge 2 ];then
                          Critical="sys is down."
                          echo $Critical|tee|mail -s "$Critical" herhan630@live.cn
                          exit 2
                  fi
  done
  }
  CheckUrl
  [root@backup ~]# sh -x 05_12_checkurl.sh 
  + CheckUrl
  + timeout=5
  + fails=0
  + success=0
  + true
  + wget --timeout=5 --tries=1 http://oldboy.b.com -q -O /dev/null
  + '[' 4 -ne 0 ']'
  + let fails=fails+1
  + '[' 0 -ge 1 ']'
  + '[' 1 -ge 2 ']'
  + true
  + wget --timeout=5 --tries=1 http://oldboy.b.com -q -O /dev/null
  + '[' 4 -ne 0 ']'
  + let fails=fails+1
  + '[' 0 -ge 1 ']'
  + '[' 2 -ge 2 ']'
  + Critical='sys is down.'
  + mail -s 'sys is down.' herhan630@live.cn
  + tee
  + echo sys is down.
  + exit 2
  [root@backup ~]# vim 05_12_checkurl.sh
  [root@backup ~]# sh -x 05_12_checkurl.sh 
  + CheckUrl
  + timeout=5
  + fails=0
  + success=0
  + true
  + wget --timeout=5 --tries=1 http://www.baidu.com -q -O /dev/null
  + '[' 0 -ne 0 ']'
  + let success=success+1
  + '[' 1 -ge 1 ']'
  + echo success
  success
  + exit 0
```

###  9.4. <a name='expr'></a>9.4 expr命令的用法
* expr命令的基本用法示例
  * expr (evaluate(求值) expressions(表达式))命令既可以用于整数运算，也可用于相关字符串长度、匹配等的运算处理
  * expr用于计算：`expr Expression`
    ```bash
      # 范例：expr命令运算用法实践
      [root@backup ~]# expr 2 + 2
      4
      [root@backup ~]# expr 2 - 2
      0
      [root@backup ~]# expr 2 * 2
      expr: 语法错误
      [root@backup ~]# expr 2 \* 2
      4
      [root@backup ~]# expr 2 / 2
      1
    ```
    * 运算符及用于计算的数字左右都至少有一个空格，否则会报错
    * 使用乘号时，必须用反斜线屏蔽其特定含义，因为Shell可能会误解星号的含义
  * expr配合变量计算
    * expr在Shell中可配合变量进行计算，但需要用反引号将计算表达式括起来
    ```bash
      # 范例：给自变量i加6
      [root@backup ~]# i=5
      [root@backup ~]# i=`expr $i + 6`
      [root@backup ~]# echo $i
      11
    ```
* expr的企业级实战案例详解
  * 判断一个变量值或字符串是否为整数
    * 实现原理是，利用以expr做计算时变量或字符串必须是整数的规则，把一个变量或字符串和一个已知的整数（非0）相加，看命令返回的值是否为0。如果为0，就认为做加法的变量或字符串为整数，否则就不是整数。
  ```bash
    # 通过expr判断变量或字符串是否为整数。
    [root@backup ~]# i=5
    [root@backup ~]# expr $i + 6 &>/dev/null
    [root@backup ~]# echo $?
    0
    [root@backup ~]# i=oldboy
    [root@backup ~]# expr $i + 6 &>/dev/null
    [root@backup ~]# echo $?
    2

    # 通过传参判断输出内容是否为整数
    [root@backup ~]# cat 05_16_expr.sh 
    #!/bin/sh
    expr $1 + 1 >/dev/null 2>&1
    [ $? -eq 0 ] &&echo int||echo chars
    [root@backup ~]# sh 05_16_expr.sh oldboy
    chars
    [root@backup ~]# sh 05_16_expr.sh 119
    int

    # 通过read读入持续等待输入例子
    [root@backup ~]# cat judge_int.sh 
    #!/bin/sh
    while true
    do
            read -p "Pls input:" a
            expr $a + 0 >/dev/null 2>&1
            [ $? -eq 0 ] && echo int || echo chars
    done
    root@backup ~]# sh judge_int.sh
    Pls input:116
    int
    Pls input:gg
    chars
    Pls input:^C

    # 将前文的混合运算小程序改成输入两个参数后进行计算的程序，并且要能判断传参的个数及通过expr判断传入的参数是否为整数
    # 编程思路：
    # 第一关，判断参数个数是否为2，若不是，则给出提示终止运行
    # 第二关，判断传入的参数是否为整数，若不是，则给出提示终止运行
    # 第三关，做运算
    [root@backup ~]# cat 05_18_1.sh 
    #!/bin/bash
    # no.1
    [ $# -ne 2 ] &&{
            echo $"USAGE $0 NUM1 NUM2"
            exit 1
    }
    # no.2
    a=$1
    b=$2
    expr $a + $b + 110 &>/dev/null
    if [ $? -ne 0 ];then
            echo "you must input two nums."
            exit 2
    fi
    # no.3
    echo "a-b=$(($a-$b))"
    echo "a+b=$(($a+$b))"
    echo "a*b=$(($a*$b))"
    echo "a/b=$(($a/$b))"
    echo "a**b=$(($a**$b))"
    echo "a%b=$(($a%$b))"
    [root@backup ~]# sh 05_18_1.sh 
    USAGE 05_18_1.sh NUM1 NUM2
    [root@backup ~]# sh 05_18_1.sh 8 2
    a-b=6
    a+b=10
    a*b=16
    a/b=4
    a**b=64
    a%b=0


    #方法2
    [root@backup ~]# cat 05_18_2.sh
    #!/bin/bash
    #no.1
    [ $# -ne 2 ] &&{
            echo $"USAGE $0 NUM1 NUM2"
            exit 1
    }
    #no.2
    a=$1
    b=$2
    expr $a + 1 &>/dev/null
    RETVAL_A=$?
    expr $b + 1 &>/dev/null
    RETVAL_B=$?
    if [ $RETVAL_A -ne 0 -o $RETVAL_B -ne 0 ];then
            echo "you must input two nums."
            exit 2
    fi
    #no.3
    echo "a-b=$(($a-$b))"
    echo "a+b=$(($a+$b))"
    echo "a*b=$(($a*$b))"
    echo "a/b=$(($a/$b))"
    echo "a**b=$(($a**$b))"
    echo "a%b=$(($a%$b))"
    [root@backup ~]# sh 05_18_2.sh
    USAGE 05_18_2.sh NUM1 NUM2
    [root@backup ~]# sh 05_18_2.sh 1 a
    you must input two nums.
    [root@backup ~]# sh 05_18_2.sh 1 2
    a-b=-1
    a+b=3
    a*b=2
    a/b=0
    a**b=1
    a%b=1

    # expr match 功能进行整数判断
    [root@backup ~]# cat t1.sh 
    if [[ `expr match "$1" "[0-9][0-9]*S"` == 0 ]];then
            echo "$1 is not a num"
    else
            echo "$1 is a num"
    fi
    [root@backup ~]# sh t1.sh a
    a is not a num
    [root@backup ~]# sh t1.sh 11
    11 is not a num
    [root@backup ~]# sh t1.sh abcd123
    abcd123 is not a num
  ```

  * expr的特殊用法：判断文件扩展命名是否符合要求
  
  ```bash
    # 通过expr判断文件扩展名是否符合要求
    [root@backup ~]# cat expr1.sh 
    #!/bin/sh
    if expr "$1" : ".*\.pub" &>/dev/null ;then
            echo "you are using $1"
    else
            echo "pls use *.pub file"
    fi
    [root@backup ~]# sh expr1.sh id_dsa.pub
    you are using id_dsa.pub
    [root@backup ~]# sh expr1.sh id_dsa
    pls use *.pub file
    
    # 使用expr命令实现系统ssh服务自带的ssh-copy-id公钥分发脚本
  ```

  * 通过expr计算字符串的长度
  ```bash
    # 利用expr计算字符串的长度
    [root@backup ~]# char="I am herhan"
    [root@backup ~]# expr length "$char"
    11
    [root@backup ~]# echo ${#char}
    11
    [root@backup ~]# echo ${char}|wc -L
    11
    [root@backup ~]# echo ${char}|awk '{print length($0)}'
    11

    # 请编写Shell脚本，打印下面语句中字符数不大于6的单词
    [root@backup ~]# cat word_length.sh 
    for n in I am herhan linux welcome to our training
    do 
            if [ `expr length $n` -le 6 ];then
                    echo $n
            fi
    done
    [root@backup ~]# sh word_length.sh
    I
    am
    herhan
    linux
    to
    our
  ```

###  9.5. <a name='bc'></a>9.5 bc命令的用法
* bc是UNIX/Linux下的计算器，因此，除了可以作为计算器来使用，还可以作为命令行计算工具使用

```bash
  # 将bc作为计算器来应用
  [root@backup ~]# bc
  bc 1.06.95
  Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006 Free Software Foundation, Inc.
  This is free software with ABSOLUTELY NO WARRANTY.
  For details type `warranty`. 
  1+1
  2
  3*3
  9

  # 将bc用在命令行下面，以实现运算功能
  [root@backup ~]# echo 5+3|bc
  8
  [root@backup ~]# echo 5.5+3.5|bc
  9.0
  [root@backup ~]# echo 5.5-3.5|bc
  2.0
  [root@backup ~]# echo "scale=2;355/113"|bc
  3.14
  [root@backup ~]# echo "scale=6;355/113"|bc
  3.141592
  # 利用bc配合变量运算
  [root@backup ~]# i=5
  [root@backup ~]# i=`echo $i+6|bc`
  [root@backup ~]# echo $i
  11

  # 通过一条命令就按输出1+2+3+...+10的表达式，并计算出结果，请使用bc命令计算
  [root@backup ~]# seq -s "+" 10
  1+2+3+4+5+6+7+8+9+10
  [root@backup ~]# echo {1..10}|tr  " " "+"
  1+2+3+4+5+6+7+8+9+10
  [root@backup ~]# echo `seq -s '+' 10`=`seq -s "+" 10|bc`
  1+2+3+4+5+6+7+8+9+10=55
  [root@backup ~]# echo `seq -s '+' 10`=$((`seq -s "+" 10`))
  1+2+3+4+5+6+7+8+9+10=55
  [root@backup ~]# echo `seq -s '+' 10`=`seq -s " + " 10|xargs expr`
  1+2+3+4+5+6+7+8+9+10=55
  [root@backup ~]# echo `seq -s '+' 10`=$(echo $[`seq -s "+" 10`])
  1+2+3+4+5+6+7+8+9+10=55
```

###  9.6. <a name='awk'></a>9.6 awk实现计算
* 利用awk进行运算的效果也很好，适合小数和整数
```bash
  [root@backup ~]# echo "7.7 3.8" |awk '{print ($1-$2)}'
  3.9
  [root@backup ~]# echo "358 113" |awk '{print ($1-3)/$2}'
  3.14159
  [root@backup ~]# echo "3 9" |awk '{print ($1+3)*$2}'
  54
```

###  9.7. <a name='declaretypeset'></a>9.7 declare(同typeset)命令用法
```bash
  [root@backup ~]# declare -i A=30 B=7
  [root@backup ~]# A=A+B
  [root@backup ~]# echo $A
  37
```

###  9.8. <a name='-1'></a>$[]符号的运算示例
```bash
  [root@backup ~]# cat yhsj.sh 
  #!/bin/bash
  if (test -z $1);then
          read -p "Input Max lines:" MAX
  else
          MAX=$1
  fi
  i=1
  while [ $i -le $MAX ]
  do
          j=1
          while [ $j -le $i ]
          do
                  f=$[i-1]
                  g=$[j-1]
                  if [ $j -eq $i ] || [ $j -eq 1 ];then
                          declare SUM_${i}_$j=1
                  else
                          declare A=$[SUM_${f}_$j]
                          declare B=$[SUM_${f}_$g]
                          declare SUM_${i}_$j=`expr $A + $B`
                  fi
                  echo -en $[SUM_${i}_$j]" " 
                  let j++
          done
          echo 
          let i++
  done
  [root@backup ~]# sh yhsj.sh
  Input Max lines:10
  1 
  1 1 
  1 2 1 
  1 3 3 1 
  1 4 6 4 1 
  1 5 10 10 5 1 
  1 6 15 20 15 6 1 
  1 7 21 35 35 21 7 1 
  1 8 28 56 70 56 28 8 1 
  1 9 36 84 126 126 84 36 9 1 
```

##  10. <a name='Shellread'></a>10 基于Shell变量输入read命令的运算实践
###  10.1. <a name='read-1'></a>10.1 read命令基础
* Shell变量除了可以直接赋值或脚本传参外，还可以使用read命令从标准输入中获得，read为bash内置命令，可以通过help read查看帮助
* 语法格式：read [参数][变量名]
  * -p prompt：设置提示信息。
  * -t timeout：设置输入等待的时间，单位默认为秒
```bash
  # 实现read的基本读入功能
  [root@backup ~]# read -t 10 -p "Pls input one num:" num
  Pls input one num:10
  [root@backup ~]# echo $num
  10
  [root@backup ~]# read -t 10 -p "Pls input two number:" a1 a2
  Pls input two number:1 2
  [root@backup ~]# echo $a1
  1
  [root@backup ~]# echo $a2
  2

  # 把前面加减乘除计算传参的脚本改成通过read方式读入整数变量
  [root@backup ~]# cat test_2.sh 
  #!/bin/bash
  read -t 15 -p "please input two number:" a b
  echo "a-b=$(($a-$b))"
  echo "a+b=$(($a+$b))"
  echo "a*b=$(($a*$b))"
  echo "a/b=$(($a/$b))"
  echo "a**b=$(($a**$b))"
  echo "a%b=$(($a%$b))"
  [root@backup ~]# sh test_2.sh
  please input two number:1 2
  a-b=-1
  a+b=3
  a*b=2
  a/b=0
  a**b=1
  a%b=1
```

###  10.2. <a name='read-1'></a>10.2 以read命令读入及传参的综合企业案例
```bash
  # 改造以上问题
  # 第一关：若用户按要求输入了两个值，则过关
  # 第二关：用户输入的内容均为整数，才能过关
  # 第三关：当读入的参数符合个数和整数条件时，进行计算
  #!/bin/bash
  read -t 15 -p "Please input two number:" a b
  #no.1
  [ ${#a} -le 0 ] && {
          echo "the first num is null"
          exit 1
  }
  [ ${#b} -le 0 ] && {
          echo "the first num is null"
          exit 1
  }
  #no.2
  expr $a + 1 &>/dev/null
  RETVAL_A=$?
  expr $b + 1 &>/dev/null
  RETVAL_B=$?
  if [ $RETVAL_A -ne 0 -o $RETVAL_B -ne 0 ];then
          echo "one of the num is not num, pls input again."
          exit 1
  fi
  #no.3
  echo "a-b=$(($a-$b))"
  echo "a+b=$(($a+$b))"
  echo "a*b=$(($a*$b))"
  echo "a/b=$(($a/$b))"
  echo "a**b=$(($a**$b))"
  echo "a%b=$(($a%$b))"
  [root@backup ~]# sh read_size01.sh 
  Please input two number:3 4
  a-b=-1
  a+b=7
  a*b=12
  a/b=0
  a**b=81
  a%b=3
```