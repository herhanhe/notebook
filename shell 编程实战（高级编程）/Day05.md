# `Shell`高级编程 Day05 —— if条件语句的知识与实践
[toc]

## 1.1 if条件语句
### 1.1.1 if条件语句的语法

* 1.单分支结构
  * 第一种语法
  ```bash
    if <条件表达式>
        then
            指令 
    fi
  ```
  * 第二种语法
  ```bash
    if <条件表达式>; then
        指令
    fi
  ```
  * if单分支语句执行流程逻辑图
  
  ![7.1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/shell%E7%BC%96%E7%A8%8B%E5%AE%9E%E6%88%98/7.1.png)

  * 嵌套的条件语句
  ```bash
    if <条件表达式>
        then
            if <条件表达式>
                then
                    指令
            fi
    fi 
  ```

  * 文件条件表达式`[ -f "$file1" ]&& echo 1`就等价于下面的if条件语句
  ```bash
    if [ -f "$file1" ];then
        echo 1
    fi
  ```
* 2.双分支结构
  * if条件语句的双分支结构语法为：
  ```bash
    if <条件表达式>
        then
            echo 1
    else
            echo 0
    fi
  ```
  * 文件测试条件表达式`[ -f "$file1" ]&& echo 1 || echo 0`就相当于下面的双分支的if条件语句
  ```bash
    if [ -f "$file1" ]
        then
            echo 1
    else 
            echo 0
    fi
  ```
  * if双分支语句执行流程逻辑图

  ![7.3](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/shell%E7%BC%96%E7%A8%8B%E5%AE%9E%E6%88%98/7.2.png)

* 3.多分支结构
  * if条件语句多分支语法
  ```bash
    if <条件表达式1>
        then
            指令1
    elif <条件表达式2>
        then
            指令2
    else
            指令3
    fi
    -------多个elif-------
    if <条件表达式1>
        then
            指令 elif <条件表达式2>
        then
            指令 elif <条件表达式3>
        then
            指令 ......
    else
            指令 fi
  ```
  * if多分支语句执行流程对应的逻辑图

  ![7.3](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/shell%E7%BC%96%E7%A8%8B%E5%AE%9E%E6%88%98/7.3.png)

### 1.1.2 if条件语句多种条件表达式语法
* (1)test条件表达式
  ```bash
    if test 表达式 then
        指令
    fi
  ```
* (2)`[]`条件表达式
  ```bash
    if [ 字符串或算术表达式 ]
        then
            指令
    fi
  ```
* (3)`[[]]`条件表达式
  ```bash
    if [[ 字符串表达式 ]]
        then
            指令
    fi
  ```
* (4)`(())`条件表达式
  ```bash
    if (( 算术表达式 ))
        then
            指令
    fi
  ```
* (5)命令表达式
  ```bash
    if 命令 then
        指令
    fi
  ```

### 1.1.3 单分支if条件语句实践
```bash
    # 把下面测试文件中条件表达式的语句改成if条件语句
    [root@backup ~]# [ -f /etc/hosts ] && echo 1
    1
    [root@backup ~]# [[ -f /etc/hosts ]] && echo 1
    1
    [root@backup ~]# test -f /etc/hosts  && echo 1
    1
    # 参考答案
    [root@backup ~]# cat 7_1.sh 
    if [ -f /etc/hosts ] 
    then
            echo "[1]"
    fi
    if [[ -f /etc/hosts ]]
    then
            echo "[[1]]"
    fi
    if test -f /etc/hosts 
    then
            echo "test1"
    fi
    #结果
    [root@backup ~]# sh 7_1.sh
    [1]
    [[1]]
    test1

    # 开发Shell脚本判断系统剩余内存的大小，如果低于100MB，就邮件报警给系统管理员，并且将脚本加入系统定时任务，即每3分钟执行一次检查
    # 对于开发程序而言，一般来说应该遵循下面的3步
    # (1)需求分析：明白开发需求，是完成程序的大前提，因此，分析需求至关重要，一切不以需求为主的程序开发，都是不提倡的
    # (2)设计思路：设计思路就是根据需求，把需求进行拆解，分模块逐步实现
    # 1)获取当前系统剩余内存的值（先在命令行实现）
    # 2)配置邮件报警（可采用第三方邮件服务器）
    # 3)判断取到的值是否小于100MB，如果小于100MB，就报警（采用if语句）
    # 4)编写实现Shell脚本
    # 5)加入crond定时任务，每三分钟检查一次
    # (3)编码实现：编码实现就是具体的编码及调试过程，工作中很可能需要现在测试环境下调试，调试好了，再发布到生产环境中。
    # 本例的最终实现过程
    # 1)获取内存大小，注意：内存大小是下面命令中对应buffers/cache那一行结尾的值（buffers）
    [root@backup ~]# free -m
                    total        used        free      shared  buff/cache   available
    Mem:            972          82         809           7          80         776
    Swap:          1023           0        1023
    [root@backup ~]# free -m|awk 'NR==2 {print $6}'
    80
    # 2)发邮件的客户端常见的有mail或mutt；服务端有sendmail服务（CentOS5下默认的）、posfix服务（CentOS6下默认的）。这里不使用本地的邮件服务，而是使用本地的mail客户端，以及第三方的邮件服务商，利用这个邮件帐号来给接受报警的人发送报警邮件。
    [root@backup ~]# echo -e "set from=hanhan630@163.com smpt=smtp.163.com\nset smtp-auth-user=hanhan630 smtp-auth-password=**** smtp-auth=login" >> /etc/mail.rc
    [root@backup ~]# tail -2 /etc/mail.rc
    set from=hanhan630@163.com smpt=smtp.163.com
    set smtp-auth-user=hanhan630 smtp-auth-password=**** smtp-auth=login
    [root@backup ~]# echo "herhan"|mail -s "title" hanhan630@163.com
    [root@backup ~]# echo "herhan" >/tmp/test.txt
    [root@backup ~]# mail -s "title" hanhan630@163.com </tmp/test.txt
    # 3)编写Shell脚本
    [root@backup ~]# cat 7_2.sh 
    #!/bin/bash
    FreeMem=`free -m|awk 'NR==2 {print $6}'`
    CHARS="Current memory is $FreeMem"
    if [ $FreeMem -lt 100 ];then
            echo $CHARS|tee /tmp/messages.txt
            mail -s "`date +%F-%T`$CHARS" test@123.com < /tmp/messages.txt
    fi
    [root@backup ~]# sh 7_2.sh
    Current memory is 82
    [root@backup ~]# crontab -l|tail -2
    */5 * * * * /usr/sbin/ntpdate ntp3.aliyun.com > /dev/null 2>&1
    */3 * * * * /bin/sh ~/7_2.sh &>/dev/null
    # 监控内存并报警有多种Shell写法
    [root@backup ~]# cat 7_2.sh 
    #!/bin/bash
    FreeMem=`free -m|awk 'NR==2 {print $6}'`
    CHARS="Current memory is $FreeMem"
    if [ $FreeMem -lt 1000 ];then
            echo $CHARS|tee /tmp/messages.txt
            mail -s "`date +%F-%T`$CHARS" test@123.com < /tmp/messages.txt
    fi
    if [[ $FreeMem -lt 1000  ]];then
            echo $CHARS|tee /tmp/messages.txt
    fi
    if (($FreeMem<1000));then
            echo $CHARS|tee /tmp/messages.txt
    fi
    if test $FreeMem -lt 1000;then
            echo $CHARS|tee /tmp/messages.txt
    fi
```
### 1.1.4 if条件语句的深入实践
```bash
    # 分别使用read读入及脚本传参的方式比较两个整数的大小
    # 参考答案1：使用单分支if语句和read读入实现整数大小的比较
    [root@backup ~]# cat 7_3_1.sh 
    #!/bin/sh
    read -p "pls input two num:" a b
    if [ $a -lt $b ];then
            echo "yes,$a less than $b"
            exit 0
    fi
    if [ $a -eq $b ];then
            echo "yes,$a equal $b"
            exit 0
    fi
    if [ $a -gt $b ];then
            echo "yes,$a greater than $b"
            exit 0
    fi
    [root@backup ~]# sh 7_3_1.sh 
    pls input two num:6 2
    yes,6 greater than 2
    [root@backup ~]# sh 7_3_1.sh 
    pls input two num:2 6
    yes,2 less than 6
    [root@backup ~]# sh 7_3_1.sh 
    pls input two num:6 6
    yes,6 equal 6

    # 参考答案2：使用多分支if语法和利用read读入变量实现整数大小的比较
    [root@backup ~]# cat 7_3_2.sh 
    #!/bin/sh
    read -p "pls input two num:" a b
    if [ $a -lt $b ];then
            echo "yes,$a less than $b"
    elif [ $a -eq $b ];then
            echo "yes,$a equal $b"
    elif [ $a -gt $b ];then
            echo "$a greater than $b"
    fi
    [root@backup ~]# sh 7_3_2.sh 
    pls input two num:8 4
    8 greater than 4
    [root@backup ~]# sh 7_3_2.sh 
    pls input two num:4 8
    yes,4 less than 8
    [root@backup ~]# sh 7_3_2.sh 
    pls input two num:8 8
    yes,8 equal 8

    # 参数答案3：用脚本传参的方式比较整数大小
    # 单分支
    [root@backup ~]# cat 7_3_3.sh 
    #!/bin/sh
    a=$1
    b=$2
    if [ $a -lt $b ];then
            echo "yes,$a less than $b"
            exit 0
    fi
    if [ $a -eq $b ];then
            echo "yes,$a equal $b"
            exit 0
    fi
    if [ $a -gt $b ];then
            echo "yes,$a greater than $b"
            exit 0
    fi
    [root@backup ~]# sh 7_3_2.sh 
    pls input two num:8 4
    8 greater than 4
    [root@backup ~]# sh 7_3_2.sh 
    pls input two num:4 8
    yes,4 less than 8
    [root@backup ~]# sh 7_3_2.sh 
    pls input two num:8 8
    yes,8 equal 8
    # 多分支实现脚本
    [root@backup ~]# cat 7_3_4.sh 
    #!/bin/sh
    a=$1
    b=$2
    if [ $a -lt $b ];then
            echo "yes,$a less than $b"
    elif [ $a -eq $b ];then
            echo "yes,$a equal $b"
    else [ $a -gt $b ]
            echo "yes,$a greater than $b"
    fi
    [root@backup ~]# sh 7_3_4.sh 2 3
    yes,2 less than 3
    [root@backup ~]# sh 7_3_4.sh 3 2
    yes,3 greater than 2
    [root@backup ~]# sh 7_3_4.sh 3 3
    yes,3 equal 3
```
## 1.2 if条件语句企业案例精讲
### 1.2.1 监控web和数据库的企业案例
* 监控web服务和MySQL数据库服务是否异常的常见方法

情况|方法
-|-
端口监控|1）在服务器本地监控服务端口的常见命令有netstat、ss、lsof<br>2）从远端监控服务器本地端口的命令有telnet、nmap、nc
监控服务进程或进程数|此方法适合本地服务器，注意，过滤的是进程的名字。命令为:<br>`ps -ef\|grep nginx\|wc -l`<br>`ps -ef\|grep mysql\|wc -l`
在客户端模拟用户访问|使用wget或curl命令进行测试（如果监测数据库，则需要转为通过web服务器去访问数据库），并对测试结果做三种判断：<br>1）利用返回值（echo $?）进行判断；<br>2）获取特殊字符串以进行判断（需要事先开发好程序）；<br>3）根据HTTP响应header的情况进行判断；
登录MySQL数据库判断|通过MySQL客户端连接数据库，根据返回值或返回内容判断。例如：`mysql -uroot -poldboy123 -e "select version();" &>/dev/null;echo $?`


```bash
    # 用if条件语句针对Nginx Web服务或MySQL数据库服务是否正常进行检测，如果服务未启动，则启动相应的服务
    #（1）分析问题：先想一想监控web服务和MySQL数据库服务是否异常的方法有哪些
    # 此外对端口进程等进行判断时，尽量先通过grep过滤端口和进程特殊标记字符串，然后结合wc将过滤到的结果转成行数再比较，这样相对简单有效，且经过wc-l命令处理之后的结果一定是数字，这样再进行判断就会比较便捷。如果
    #（2）监控MySQL数据库异常
    #!/bin/sh
    echo method1--------------
    if [ `netstat -lnt|grep 3306|awk -F "[ :]+" '{print $5}'` -eq 3306 ];then
            echo "MySQL is Running."
    else
            echo "MySQL is Stopped."
            systemctl start mariadb
    fi

    echo method2--------------
    if [ `netstat -lnt|grep 3306|awk -F "[ :]+" '{print $5}'` = "3306" ];then
            echo "MySQL is Running."
    else
            echo "MySQL is Stopped."
            systemctl start mariadb
    fi

    echo method3--------------
    if [ `netstat -lntup|grep mysql|wc -l` -gt 0 ];then
            echo "MySQL is Running."
    else
            echo "MySQL is Stopped."
            systemctl start mariadb
    fi

    echo method4--------------
    if [ `lsof -i tcp:3306|wc -l` -gt 0 ];then
            echo "MySQL is Running."
    else
            echo "MySQL is Stopped."
            systemctl start mariadb
    fi

    echo method5--------------
    [ `rpm -qa nmap|wc -l` -lt 1 ] && yum install nmap -y &>/dev/null
    if [ `nmap 127.0.0.1 -p 3306 2>/dev/null|grep open|wc -l` -gt 0 ];then
            echo "MySQL is Running."
    else
            echo "MySQL is Stopped."
            systemctl start mariadb
    fi

    echo method6--------------
    [ `rpm -qa nc|wc -l` -lt 1 ] && yum install nc -y &>/dev/null 
    if [ `nc -w 2 127.0.0.1 3306 &>/dev/null&&echo ok|grep ok|wc -l` -gt 0 ];then
            echo "MySQL is Running."
    else
            echo "MySQL is Stopped."
            systemctl start mariadb
    fi

    echo method7--------------
    if [ `ps -ef|grep -v grep|grep mysql|wc -l` -gt 0 ];then
            echo "MySQL is Running."
    else
            echo "MySQL is Stopped."
            systemctl start mariadb
    fi
    # (3)监控NginxWeb服务异常
    #!/bin/sh
    echo http method1--------------------
    if [ `netstat -lnt|grep 80|awk -F "[ :]+" '{print $5}'` -eq 80 ];then
            echo "Nginx is Running."
    else
            echo "Nginx is Stopped."
            systemctl start nginx
    fi

    echo http method2--------------------
    if [ "`netstat -lnt|grep 80|awk -F "[ :]+" '{print $5}'`" = "80" ];then
            echo "Nginx is Running."
    else
            echo "Nginx is Stopped."
            systemctl start nginx
    fi

    echo http method3--------------------
    if [ `netstat -lntup|grep nginx|wc -l` -gt 0 ];then
            echo "Nginx is Running."
    else
            echo "Nginx is Stopped."
            systemctl start nginx
    fi

    echo http method4--------------------
    if [ `lsof -i tcp:80|wc -l` -gt 0 ];then
            echo "Nginx is Running."
    else
            echo "Nginx is Stopped."
            systemctl start nginx
    fi

    echo http method5--------------------
    [ `rpm -qa nmap|wc -l` -lt 1 ] && yum install nmap -y &>/dev/null
    if [ `nmap 127.0.0.1 -p 80 2>/dev/null|grep open|wc -l` -gt 0 ];then
            echo "Nginx is Running."
    else
            echo "Nginx is Stopped."
            systemctl start nginx
    fi

    echo http method6--------------------
    [ `rpm -qa nc|wc -l` -lt 1 ] && yum install nc -y &>/dev/null
    if [ `nc -w 2 127.0.0.1 80 &>/dev/null&&echo ok|grep ok|wc -l` -gt 0 ];then
            echo "Nginx is Running."
    else
            echo "Nginx is Stopped."
            systemctl start nginx
    fi

    echo http method7--------------------
    if [ `ps -ef|grep -v grep|grep nginx|wc -l` -ge 1 ];then
            echo "Nginx is Running."
    else
            echo "Nginx is Stopped."
            systemctl start nginx
    fi

    echo http method8--------------------
    if [[ `curl -I -s -o /dev/null -w "%{http_code}\n" http://127.0.0.1` =~[23]0[012] ]];then
            echo "Nginx is Running."
    else
            echo "Nginx is Stopped."
            systemctl start nginx
    fi

    echo http method9--------------------
    if [ `curl -I http://127.0.0.1 2>/dev/null|head -l|egrep "200|302|301"|wc -l` -eq 1 ];then
            echo "Nginx is Running."
    else
            echo "Nginx is Stopped."
            systemctl start nginx
    fi

    echo http method10--------------------
    if [ "`curl -s http://127.0.0.1`" = "herhan" ];then
            echo "Nginx is Running."
    else
            echo "Nginx is Stopped."
            systemctl start nginx
    fi
```
### 1.2.2 更多的生产场景实战案例
```bash
    # 监控memcached服务是否正常，模拟用户（web客户端）监测
    #!/bin/sh
    printf "del key\r\n"|nc 127.0.0.1 11211 &>/dev/null
    printf "set key 0 0 10 \r\nherhan1234\r\n"|nc 127.0.0.1 11211 &>/dev/null
    McValues=`printf "get key\r\n"|nc 127.0.0.1 11211|grep herhan1234|wc -l`
    if [ $McValues -eq 1 ];then
            echo "memcached status is ok"
    else
            echo "memcached status is bad"
    fi

    # 开发rsync服务的启动脚本
    # 1）分析问题。就需要熟悉rsync服务的配置，以及rsync服务是如何启动，以及如何停止的
    # 2）要实现/etc/init.d/rsyncd {start|stop|restart}的语法
    # 第1步，rsync服务的简单注备
    # 第2步，实现rsync服务的启动和停止的方法
    # 停止方法：
    [root@backup ~]# rsync --daemon
    [root@backup ~]# netstat -lnt|grep 873
    tcp        0      0 0.0.0.0:873             0.0.0.0:*               LISTEN     
    tcp6       0      0 :::873                  :::*                    LISTEN     
    # 启动方法：
    [root@backup ~]# pkill rsync
    [root@backup ~]# netstat -lnt|grep 873
    # 第3步，判断rsync服务是否启动的方法
    # 常规方法有检测端口以及进程是否存在，还可以当服务启动时，创建一个锁文件（/var/lock/subsys/）,而当服务停止时，就删除这个锁文件，这样就可以通过判断这个文件有无，来确定服务是否是启动状态，这是一些系统脚本常用的手法。
    # 第4步，开发rsync服务的启动脚本。
    [root@backup ~]# cat /etc/init.d/rsynced 
    #!/bin/sh
    if [ $# -ne 1 ];then
            echo $"usage:$0 {start|stop|restart}"
            exit 1
    fi
    if [ "$1" = "start" ];then
            rsync --daemon
            sleep 2
            if [ `netstat -lntup|grep rsync|wc -l` -ge 1 ];then
                    echo "rsyncd is started."
                    exit 0
            fi
    elif [ "$1" = "stop" ];then
            killall rsync &>/dev/null
            sleep 2
            if [ `netstat -lntup|grep rsync|wc -l` -eq 0 ];then
                    echo "rsyncd is stopped."
                    exit 0
            fi
    elif [ "$1" = "restart" ];then
            killall rsync
            sleep 1
            killpro=`netstat -lntup|grep rsync|wc -l`
            rsync --daemon
            sleep 1
            startpro=`netstat -lntup|grep rsync|wc -l`
            if [ $killpro -eq 0 -a $startpro -ge 1 ];then
                    echo "rsyncd is restarted."
                    exit 0
            fi
    else
            echo $"usage:$0 {start|stop|restart}"
            exit 1
    fi
    [root@backup ~]# /etc/init.d/rsynced start
    rsyncd is started.
    [root@backup ~]# netstat -lnt|grep 873
    tcp        0      0 0.0.0.0:873             0.0.0.0:*               LISTEN     
    tcp6       0      0 :::873                  :::*                    LISTEN     
    [root@backup ~]# /etc/init.d/rsynced stop
    rsyncd is stopped.
    [root@backup ~]# netstat -lnt|grep 873
    [root@backup ~]# /etc/init.d/rsynced restart
    rsyncd is restarted.
    [root@backup ~]# netstat -lnt|grep 873
    tcp        0      0 0.0.0.0:873             0.0.0.0:*               LISTEN     
    tcp6       0      0 :::873                  :::*                    LISTEN

    # 使用上述开发好的rsync服务启动脚本，实现通过chkconfig来管理开机自启动
    # chkconfig命令是系统用来管理脚开机自启动的命令，但是需要脚本支持chkconfig管理才行，具体的方法是在脚本的开头解释其之后加入如下内容：
    # chkconfig: 2345 20 80
    # description: Saves and restores system entropy pool
    # 两行内容的开头都要有#号
    # 第一行是说需要chkconfig管理，2345是Linux运行级别，表示该脚本默认在2345级别为启动状态，20是脚本的开始启动顺序，80是脚本的停止顺序，这两个数字都是不超过99的数字，一般情况下，可以根据服务的启动需求来选择，应用服务一般要靠后启动为佳，越早停止越好。
    [root@backup ~]# cat /etc/init.d/rsynced 
    #!/bin/sh
    # chkconfig: 2345 20 80
    # description: Rsyncd Startup scripts by herhan.
    if [ $# -ne 1 ];then
            echo $"usage:$0 {start|stop|restart}"
            exit 1
    fi
    if [ "$1" = "start" ];then
            rsync --daemon
            sleep 2
            if [ `netstat -lntup|grep rsync|wc -l` -ge 1 ];then
                    echo "rsyncd is started."
                    exit 0
            fi
    elif [ "$1" = "stop" ];then
            killall rsync &>/dev/null
            sleep 2
            if [ `netstat -lntup|grep rsync|wc -l` -eq 0 ];then
                    echo "rsyncd is stopped."
                    exit 0
            fi
    elif [ "$1" = "restart" ];then
            killall rsync
            sleep 1
            killpro=`netstat -lntup|grep rsync|wc -l`
            rsync --daemon
            sleep 1
            startpro=`netstat -lntup|grep rsync|wc -l`
            if [ $killpro -eq 0 -a $startpro -ge 1 ];then
                    echo "rsyncd is restarted."
                    exit 0
            fi
    else
            echo $"usage:$0 {start|stop|restart}"
            exit 1
    fi
    [root@backup ~]# chkconfig --list rsyncd
    注：该输出结果只显示 SysV 服务，并不包含
    原生 systemd 服务。SysV 配置数据
    可能被原生 systemd 配置覆盖。 

        要列出 systemd 服务，请执行 'systemctl list-unit-files'。
        查看在具体 target 启用的服务请执行
        'systemctl list-dependencies [target]'。

    服务 rsyncd 支持 chkconfig，但它未在任何运行级别中被引用（请运行“chkconfig --add rsyncd”）
    [root@backup ~]# chkconfig --add rsyncd
    [root@backup ~]# chkconfig --list rsyncd
    注：该输出结果只显示 SysV 服务，并不包含
    原生 systemd 服务。SysV 配置数据
    可能被原生 systemd 配置覆盖。 

        要列出 systemd 服务，请执行 'systemctl list-unit-files'。
        查看在具体 target 启用的服务请执行
        'systemctl list-dependencies [target]'。

    rsyncd          0:关    1:关    2:开    3:开    4:开    5:开    6:关
```

