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

### 2.2 显示与取消环境变量
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
### 2.3 环境变量知识小结
* 变量名通常要大写
* 变量可以在自身的Shell及子Shell中使用
* 常用export来定义环境变量
* 执行env默认可以显示所有的环境变量名称及对应的值
* 输出时用“$变量名”，取消时用“unset变量名”
* 书写crond定时任务时要注意，脚本要用到的环境变量最好先在所执行的Shell脚本中重新定义
* 如果希望环境变量永久生效，则可以将其放在用户环境变量文件或全局变量文件里

### 2.4 环境变量初始化与对应文件的生效顺序
* 系统环境文件：在登录Linux系统并启动一个bash shell时，默认情况下bash会在若干个文件中查找环境变量的设置，这些文件称为系统环境文件。
* bash检查的环境变量文件的情况取决于系统运行Shell的方式
  * 1）通过系统用户登录后默认运行的Shell
  * 2）非登录交互式运行Shell
  * 3）执行脚本运行非交互式Shell
* 第一种方式下的Shell加载环境变量的顺序
  ![3-1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/shell%E7%BC%96%E7%A8%8B%E5%AE%9E%E6%88%98/3.1.png)
* 第二种方式下的Shell加载环境变量的顺序
  * 非登录Shell只会加载`$HOME/.bashrc`(用户环境变量文件)，并会去找`/etc/bashrc`(全局环境变量文件)。

## 3.普通变量
### 3.1 定义本地变量
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
  tar: Removing leading `/' from member names
  [root@herhan ~]# ls     # 打包结果，包名中包含有当前日期
  etc_2021-07-04.tar.gz
  [root@herhan ~]# H=$(uname -n)
  [root@herhan ~]# echo $H
  herhan
  [root@herhan ~]# tar zcf $H.tar.gz /etc/services
  tar: Removing leading `/' from member names
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
### 3.2 变量定义及变量输出说明
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

## 4.变量定义技巧总结
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