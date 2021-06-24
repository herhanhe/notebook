<!--
 * @Author: herhan
 * @Date: 2020-08-21 12:24:57
 * @LastEditTime: 2020-08-30 01:17:08
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \github\shell 编程实战（高级编程）\Day02.md
--> 
# `Shell`高级编程 Day02

<!-- TOC -->

- [`Shell`高级编程 Day02](#shell高级编程-day02)
  - [1.`shell`](#1shell)
  - [2.`shell` 脚本](#2shell-脚本)
  - [3.`shell`脚本在运维工作中的作用地位](#3shell脚本在运维工作中的作用地位)
  - [4.脚本语言的种类](#4脚本语言的种类)
    - [4.1 `Shell`脚本语言的种类](#41-shell脚本语言的种类)
    - [4.2 其他常用的脚本语言种类](#42-其他常用的脚本语言种类)
    - [4.3 `Shell`脚本语言的优势](#43-shell脚本语言的优势)
  - [5 常用操作系统默认的`Shell`](#5-常用操作系统默认的shell)
  - [6 `Shell`脚本的建立和执行](#6-shell脚本的建立和执行)
    - [6.1 `Shell`脚本的建立](#61-shell脚本的建立)
    - [6.2 `Shell`脚本的执行](#62-shell脚本的执行)
    - [6.3 shell脚本开发的基本规范及习惯](#63-shell脚本开发的基本规范及习惯)

<!-- /TOC -->

## 1.`shell`

* `shell`是一个命令解释器，它在操作系统的最外层，负责直接与用户对话，把用户的输入解释个操作系统，并处理各种各样的操作系统的输出结果，输出到屏幕返回给用户。
![1](https://ae01.alicdn.com/kf/H7afbd1b555db4aeb890aa50c230532d58.png)

## 2.`shell` 脚本

* 1.由命令、变量和流程控制语句的组成
  * 范例2-1：在`Windows`下利用`bat`批处理程序备份网站及数据库数据的脚本。

    ```powershell
        @echo off
        set date=%date:~0,4%-%date:~5,2%-%date:~8,2%  #<==定义时间变量。
        mysqldump -uroot -p123456 -A -B > D:\bak\"%date%".sql   #<==备份数据库数据
        rar.exe a -k -r -s -ml D:\bak\"%date%".sql.rar  D:\bak\"%date%".sql  #<==打包备份出来的数据库数据
        del D:\bak\*.sql  #<==删除未打包的无用数据库数据
        rar.exe a -k -r -s -ml D:\bak\"%date%"htdocs.rar D:\work\PHPnow\htdocs  #<==打包站点目录了的数据
    ```
  
  * 范例2-2：清除`/var/log`下`messages`日志文件的简单命令脚本。

    ```shell
        # 清除日志脚本，版本 1
        cd /var/log
        cat /dev/null>messages
        echo  "Logs cleaned up."
    ```

    * **提示：/var/log/messages是Linux系统的日志文件，很重要。**
    * 范例2-2所示的脚本其实是有一些问题的，具体如下：
      * 1）如果不是`root`用户，则无法执行脚本清理日志，并且会提示系统的权限报错信息。
      * 2）没有任何流程控制语句，简单地说就是只进行顺序操作，没有成功判断和逻辑严密性。
  * 范例2-3：写一个包含命令、变量和流程控制的语言来清除`/var/log`下`messages`日志文件的`Shell`脚本

    ```shell
        #!/bin/bash
        # 清除日志脚本，版本 2
        LOG_DIR=/var/log
        ROOT_UID=0    #<==$UID为0的用户，即root用户
        # 脚本需要使用root用户权限来运行，因此，对当前用户进行判断，对不合要求的用户给出友好提示，并终止程序运行。
        if [ "$UID" -ne "$ROOT_UID" ]   #<==如果当前用户不是root，则不允许执行脚本。
          then
            echo "Must be root to run this script."   #<==给出提示后退出。
            exit  1   #<==退出脚本。
        fi
        # 如果切换到指定目录不成功，则给出提示，并终止程序运行。
        cd %LOG_DIR  || {
            echo "Cannot change to necesary directory."
            exit 1
        }  
        # 经过上述两个判断后，此处的用户权限和路径应该就是对的了，只有清空成功，才打印成功提示。
        cat /dev/null>messages  && {
            echo "Logs cleand up."
            exit 0    #  退出之前返回0表示成功，返回1表示失败。
        }
        echo "Logs cleand up fail."
        exit 1
    ```

* 2.编写`shell`要学会类似打游戏过关的环节：
  * 1.第一关 必须是`root`才能执行脚本，否则退出。
  * 2.第二关 成功切换目录（`cd /var/log`）,否则退出。
  * 3.第三关 清理日志（`cat /dev/null > messages`）,清理成功，在输出。
  * 4.第四关 通关了。。。（`echo`输出）

## 3.`shell`脚本在运维工作中的作用地位

* `shel`脚本很擅长**处理纯文本类型**的数据，而`linux`系统中几乎所有的配置文件、日志文件（如`nfs、rsync、httpd、nginx、lvs、mysql等`）、多数启动文件都是纯文本类型的文件。因此，如果学好`shell`脚本语言，就可以利用它在`linux`系统中发挥巨大的作用。

## 4.脚本语言的种类
### 4.1 `Shell`脚本语言的种类
* `Shell`脚本语言是**弱类型语言（无须定义变量的类型即可使用）**，在Unix/Linux中主要两大类：
  * `Bourne shell`: 
    * `Bourne shell(sh)`
    * `Korn shell(ksh)`
    * `Bour ne Again Shell(bash)`
  * `C shell`:
    * `csh`
    * `tsch`
* 查看系统支持的`shell`
```shell
    [herhan@localhost ~]$ cat /etc/shells 
    /bin/sh         #<==这是Linux里常用的Shell,指向/bin/bash
    /bin/bash       #<==这是Linux里常用的Shell,也是默认使用的shell
    /sbin/nologin   #<==这是Linux里常用的Shell,用于禁止用户登录
    /usr/bin/sh
    /usr/bin/bash
    /bin/tcsh
    /bin/csh
```

### 4.2 其他常用的脚本语言种类
* 1 `PHP`语言
  * `PHP`是网页程序语言，也是脚本语言。它是一款更专注于`Web`页面开发（前端展示）的语言，例如：`wordpress`、`dedecms`、`discuz`等著名的开源产品都是`PHP`语言开发的。用`PHP`程序也可以处理系统日志，配置文件等。
* 2 `Perl`语言
  * `Perl`脚本语言比`Shell`脚本语言强大很多，在2010年以前很流行，它的语法灵活、复杂，在实现不同的功能时可以多种不同的方式，缺点不易读，团队协作困难，但它仍不失为一种很好的脚本语言，存世的大量相关程序软件（比如，`xtrabakup`热备工具，`MySQL MHA`集群高可用软件等）中都有`Perl`语言的身影。
* 3.`Python`语言
  * `Python`是近几年非常流行的语言，它不但可以用于脚本程序开发，也可以实现`Web`页面程序开发（例如，`CMDB`管理系统），甚至还可以实现软件的开发（例如OpenStack、SaltStack都是Python语言开发的）、游戏开发、发数据开发、移动端开发。

### 4.3 `Shell`脚本语言的优势
* **简单、易用、高效的三大基本原则。** 

## 5 常用操作系统默认的`Shell`
* 常用的操作系统中，
  * `Linux`下默认的`Shell`是`Bource Again shell(bash)`;
  * `Solaris`和`FreeBSD`下默认的`Shell`是`Bource shell(sh)`;
  * `AIX`下默认的`Shell`是`Korn Shell(ksh)`
* 企业面试日：`CentOS Linux`系统默认的`Shell`是什么
* 通过一下方法可以查看
  * 方法1：
    ```shell
      [root@localhost ~]# echo $SHELL
      /bin/bash
    ```
  * 方法2：
    ```shell
      [root@localhost ~]# grep root /etc/passwd
      root:x:0:0:root:/root:/bin/bash
      operator:x:11:0:operator:/root:/sbin/nologin
    ```
  * **提示：结尾的`/bin/bash`就是用户登录后的`Shell`解释器**

## 6 `Shell`脚本的建立和执行
### 6.1 `Shell`脚本的建立
  ```shell
    [root@localhost ~]# echo "alias vi='vim'" >> /etc/profile  
    [root@localhost ~]# tail -l /etc/profile
                . "$i"
            else
                . "$i" >/dev/null
            fi
        fi
    done

    unset i
    unset -f pathmunge
    alias vi='vim'
    [root@localhost ~]# source /etc/profile
  ```
* 1.脚本开头(第一还)
  * 一个规范的`Shell`脚本在第一行会指出由哪个程序(解释器)来执行脚本中的内容,这一行内容在`Linux bash`的编程一般为:
    ```shell
      #!/bin/bash
      或
      #!/bin/sh  #<==255个字符以内
    ```
  * 开头的"#!"字符又称为幻数(其实叫什么都无所谓,知道它的作用就好),在执行`bash`脚本的时候,内核会根据"#!"后的解释器来确定该用哪个程序解释这个脚本中的内容.
  * 这一行必须位于每个脚本顶端的第一行,如果不是第一行则为脚本注释行.
    ```shell
      [root@localhost ~]# cat test.sh
      #!/bin/bash
      echo "herhan start"
      #!/bin/bash     #<==写到这里就是注释了
      #!/bin/sh       #<==写到这里就是注释了
      echo "herhan end"
    ```
* 2.`bash`与`sh`的区别
  * `sh`为`bash`的软链接,大多数情况下,脚本的开头使用`"#!/bin/bash"`和`"#!/bin/sh"`是没有区别的,但更规范的写法是在脚本的开头使用`"#!/bin/bash"`
    ```shell
      [root@localhost ~]# ll /bin/sh
      lrwxrwxrwx. 1 root root 4 Aug 23 11:43 /bin/sh -> bash
      [root@localhost ~]# ll /bin/bash
      -rwxr-xr-x. 1 root root 964608 Oct 30  2018 /bin/bash
    ```
  * 下面的`Shell`脚本是系统的软件启动脚本的开头部分
    ```shell
      [root@localhost ~]# head -1 /etc/init.d/network 
      #! /bin/bash
    ```

  * 如果使用`/bin/sh`执行脚本出现异常,那么可以再使用`/bin/bash`试一试,但是一般不会发生此类情况

  * 一般情况下,在安装`Linux`系统时会自动安装好`bash`软件,查看系统的bash版本:
    ```shell
      [root@localhost ~]# cat /etc/redhat-release 
      CentOS Linux release 7.6.1810 (Core)   #<==这里显示的是作者写作的`Linux`的环境版本
      [root@localhost ~]# bash --version
      GNU bash, version 4.2.46(2)-release (x86_64-redhat-linux-gnu)   
                                            #<==这里显示的是`bash`的版本
      Copyright (C) 2011 Free Software Foundation, Inc.
                                            #<==下面几行是自由软件提示的相关信息
      License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
      This is free software; you are free to change and redistribute it.
      There is NO WARRANTY, to the extent permitted by law.
    ```
  * `bash`软件曾经爆出了严重漏洞(破壳漏洞),凭借此漏洞,攻击者可能会接管计算机的整个操作系统,得以访问各种系统内的机密信息,并对系统进行更改等.任何人的计算机系统,如果使用了`bash`软件,都需要打上补丁.
    * 检测系统是否存在漏洞方法为:
      ```shell
        [root@localhost ~]# env x='() {:;}; echo be careful' bash -c "echo this is a test"
        this is a test
      ```
    * 返回如下两行,则表示需要尽快升级`bash`
      ```shell
        be careful
        this is a test
      ```
    * 升级方法为:
      ```shell
        [root@localhost ~]# yum -y update bash
        [root@localhost ~]# rpm -qa bash
        bash-4.2.46-34.el7.x86_64
      ```
  * 下面是Linux中常用脚本开头的写法
    ```shell
      #!/bin/sh
      #!/bin/bash
      #!/usr/bin/awk
      #!/bin/sed
      #!/usr/bin/tcl
      #!/usr/bin/expect   #<==expect解决交互式的语言开头解释器
      #!/usr/bin/perl   #<==perl语言解释器
      #!/usr/bin/env python   #<==python语言解释器
    ```
* 3.脚本注释
  * 在`Shell`脚本中,跟在`#`后面的内容表示注释,用来对脚本进行注释说明,注释部分不会被当作程序来执行,仅仅是给开发者和使用者看的
  * 注释可自成一行,也可以跟在脚本命令的后面与命令在同一行

### 6.2 `Shell`脚本的执行
* 1.执行顺序
  * 当`Shell`脚本运行时,它会先查找系统环境变量`ENV`,该变量指定了环境文件(加载顺序通常是`/etc/profile`,`~/.bash_profile`,`~/.bashrc`,`/etc/bashrc`等)
  * 在加载了上述环境变量文件后,`Shell`就开始执行`Shell`脚本中的内容
    * `Shell`脚本是从上至下从左至右依次执行每一行的命令及语句的,即执行完了一个命令后再执行下一个,如果在`Shell`脚本中遇到子脚本(即脚本嵌套)时,就会先执行子脚本的内容,完成后再返回父脚本继续执行父脚本内后续的命令及语句.
    * 通常情况下,在执行`Shell`脚本时,会向系统内核请求启动一个新的进程,以便在该进程中执行脚本的命令及子`Shell`脚本.
    ![2-1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/shell%E7%BC%96%E7%A8%8B%E5%AE%9E%E6%88%98/2.1.png)
  * **特殊技巧:设置`Linux`的`crond`任务时,最好能在定时任务脚本中重新定义系统环境变量,否则,一些系统环境变量将不会被加载,这个问题需要注意**
* 2.`Shell`脚本的执行通常可以采用以下几种方式.
  * 1）`ash script-name`或`sh script-name`:这是当脚本文件本身没有可执行权限时常使用的方法，或者脚本文件开头没有指定解释器时需要使用的方法。
  * 2）`path/script-name`或`./script-name`:指在当前路径下执行脚本，需要将脚本文件的权限先改为可执行。
  * 3）`source script-name`或`.script-name`:这种方法通常是使用`source`或`.`(点号)读入或加载指定的shell脚本文件，通过`source`或`.`加载执行过的脚本，由于是在当前`shell`中执行脚本。因此在脚本结束之后，脚本中的变量（包含函数）值在当前Shell中依然存在，而sh和bash执行脚本都会启动新的子Shell执行，执行后退回到父Shell。**`source`或`.`命令的功能是：在当前shell中执行source或`.`加载并执行的相关脚本文件中的命令及语句，而不是产生一个子shell来执行文件中的命令。** 
  * 4）`sh<script-name`或`cat scripts-name|sh`:
  ```bash
      # 范例
      # 创建脚本test.sh
      [root@herhan ~]# cat >test.sh<<EOF
      > echo 'I am herhan'
      > EOF
      [root@herhan ~]# cat test.sh
      echo 'I am herhan'
      
      # 第一种方法
      [root@herhan ~]# bash test.sh 
      I am herhan
      [root@herhan ~]# sh test.sh 
      I am herhan

      # 第二种方法
      [root@herhan ~]# ./test.sh
      -bash: ./test.sh: Permission denied
      [root@herhan ~]# chmod +x test.sh 
      [root@herhan ~]# ./test.sh
      I am herhan

      # 第三种方法
      [root@herhan ~]# . test.sh 
      I am herhan
      [root@herhan ~]# source test.sh 
      I am herhan

      [root@herhan ~]# echo 'userdir=`pwd`' >testsource.sh  #<==第一行的内容通常用echo处理更方便
      [root@herhan ~]# cat testsource.sh  #<==定义了一个命令变量，内容是打印当前路径。注意，打印命令用反引号
      userdir=`pwd`
      [root@herhan ~]# sh testsource.sh  #<==采用sh命令执行脚本
      [root@herhan ~]# echo $userdir  #<==此处为空，并没有出现当前路径/root的输出

      [root@herhan ~]# . testsource.sh  #<==采用source执行同一脚本
      [root@herhan ~]# echo $userdir  #<==此处输出了当前路径/root
      /root

      # 第四种方法
      [root@herhan ~]# chmod -x test.sh 
      [root@herhan ~]# ls -l test.sh 
      -rw-r--r-- 1 root root 19 Jun 24 12:34 test.sh
      [root@herhan ~]# cat test.sh 
      echo 'I am herhan'
      [root@herhan ~]# sh<test.sh
      I am herhan
      [root@herhan ~]# cat test.sh | bash
      I am herhan
  ```

### 6.3 shell脚本开发的基本规范及习惯
* 1)shell脚本的第一行是指定脚本解释器，通常为：
  ```shell
    #!/bin/bash或#!/bin/sh
  ```
* 2)shell脚本的开头会加版本、版权等信息，可修改`~/.vimrc`配置文件配置vim编辑文件时自动加上以上信息的功能
  ```shell
    # Date:
    # Auther:
    # Blog:
    # Desciption:
    # Version:
  ```
* 3)在Shell脚本中尽量不用中文
* 4)Shell脚本的命名应以.sh为扩展名
* 5)Shell脚本应存放在固定的路径下
===========习惯=============
* 1)成对的符号应尽量一次性写出来，然后退格在符号里增加内容，以防止遗漏。
* 2)对于流程控制语句，应一次性将格式写完，载添加内容。
* 3)通过缩进让代码更易读
* 4)对于常规变量的字符串定义变量值应加双引号，并且等号前后不能有空格，需要强引用的（指所见即所得的字符引用），则用单引号（''），如果是命令的引用，则用反引号（``）.
* 5)脚本中的单引号、双引号及反引号必须为英文状态下的符号，其实所有的Linux字符及符号都应该是英文状态下的符号。