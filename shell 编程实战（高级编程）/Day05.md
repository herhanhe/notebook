# `Shell`高级编程 Day05 —— if条件语句的知识与实践

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
