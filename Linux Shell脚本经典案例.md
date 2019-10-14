---
title: Linux Shell脚本经典案例
tags: 学习
notebook: shell
---
# Linux Shell脚本经典案例
## 1、获取随机字符串或数字
* 获取随机8位字符串：
    * 方法1：
    ```shell
         echo $RANDOM|md5sum|cut -c 1-8
    ```
    * 方法2：
    ```shell
         openssl rand -base64 4
    ```
    * 方法3：
    ```shell
         cat /proc/sys/kernel/random/uuid|cut -c 1-8
    ```
* 获取随机8位数字：(cksum:打印CRC效验和统计字节)
    * 方法1：
    ```shell
         echo $RANDOM|cksum|cut -c 1-8
    ```
    * 方法2：
    ```shell
         openssl rand -base64 4|cksum|cut -c 1-8
    ```
    * 方法3：
    ```shell
         date +%N|cut -c 1-8
    ```
## 2、定义一个颜色输出字符串函数
* 方法1：
```shell
    function echo_color() {
        if [ $1 == "green" ]; then
            echo -e "\033[32;40m$2\033[0m"
        elif [ $1 == "red" ]; then
            echo -e "\033[31;40m$2\033[0m"
        fi
    }
```
* 方法2：
```shell
    function echo_color() {
        case $1 in
        green)
            echo -e "[32;40m$2[0m"
            ;;
        red)
            echo -e "[31;40m$2[0m"
            ;;
        *)
            echo "Example:echo_colorred string"
        esac
    }
```
## 3、批量创建用户
```shell
    #!/bin/bash
    DATE=$(date +%F_%T)
    USER_FILE=user.txt
    echo_color(){
        if [ $1 == "green" ]; then
            echo -e "[32;40m$2[0m"
        elif [ $1 == "red" ]; then
            echo -e "[31;40m$2[0m"
        fi
    }
    #如果用户文件存在并且大小大于0就备份
    if [ -s $USER_FILE ]; then
        mv $USER_FILE $USER_FILE-$DATE.bak
        echo_color green "$USER_FILE exist,rename ${USER_FILE}-${DATE}.bak"
    fi
    echo -e "User Password" >> $USER_FILE
    echo "----------------" >> $USER_FILE
    for USER in user{1..10}; 
    do
        if ! id $USER &> /dev/null; then
            PASS=$(echo $RANDOM|md5sum|cut -c 1-8)
            useradd $USER
            echo $PASS |password --stdin $USER &> /dev/dell
            echo -e "$USER $PASS" >> $USER_FILE
            echo "$USER User create successful."
        else 
            echo_color red "$USER User already exists!"
        fi
    done 
```
## 4、检查`rpm`软件包是否安装
```shell
    #!/bin/bash
    if rpm -q sysstat &> /dev/null; then
        echo "sysstat is already installed."
    else
        echo "sysstat is not installed!"
    fi
```
## 5、检查服务状态
```shell
    #!/bin/bash
    PORT_C=$(ss -ant|grep -c 6443)
    PS_C=$(ps -df|grep kube-apiserver|grep -vc grep)
    if [ $PORT_C -eq 0 -o $PS_C -eq 0 ]; then
        echo "kube-apiserver service dowmped"
    else
        echo "kube-apiserver service running!"
    fi
```
## 6、检查主机存活状态
* 方法1：将错误`IP`放到数组里面判断是否`ping`失败三次
```shell
    #!/bin/bash
    for IP in $IP_LIST; 
    do
        NUM=1
        while [ $NUM -le 3 ];
        do
            if ping -c 1 $IP &> /dev/null; then
                echo "$IP Ping is successful."
                break
            else
                FAIL_COUNT[$NUM]=$IP
                let NUM++
            fi
        done
        if [ ${#FAIL_COUNT[*]} -eq 3 ]; then
            echo "${FAIL_COUNT[1]} Ping is failure!"
            unset FAIL_COUNT[*]
        fi
    done 
```
* 方法2：将错误次数放到`FAIL_COUNT`变量里面判断是否`ping`失败三次
```shell
    #!/bin/bash
    for IP in $IP_LIST;
    do
        FAIL_COUNT=0
        for (( i=1;i<=3;i++));
        do
            if ping -c 1 $IP &> /dev/null; then
                echo "$IP Ping is successful."
                break
            else
                let FAIL_COUNT++
            fi
        done
        if [ $FAIL_COUNT -eq 3 ]; then
            echo "$IP Ping is failure!"
        fi
    done
```
* 方法3：利用`for`循环将`ping`通就跳出循环继续，如果不跳出就会走到打印`ping`失败
```shell
    #!/bin/bash
    ping_success_status() {
        if ping -c 1 $IP &> /dev/null; then
            echo "$IP Ping is successful."
            continue
        fi
    }
    for IP in $IP_LIST; 
    do
        ping_success_status
        ping_success_status
        ping_success_status
        echo "$IP Ping is failure!"
    done
```
## 7、监控`CPU`、内存和硬盘利用率
```shell
    #!/bin/bash
    DATE=$(date +%F" "%H:%M)
    IP=$(ifconfig eth0 | awk '/netmask/{print $2}')
    if ! which vmstat &> /dev/null; then
        echo "vmstat command no found,Please isntall procps package."
        exit 1
    fi
    ## CPU
    US=$(vmstat | awk 'NR==3 {print $13}')
    SY=$(vmstat | awk 'NR==3 {print $14}')
    IDLE=$(vmstat | awk 'NR==3 {print $15}')
    WAIT=$(vmstat | awk 'NR==3 {print $15}')
    USE=$(($US+$SY))
    if [ $USE -ge 50 ]; then
        echo "
            Date:$DATE
            Host:$IP
            Problem:CPU utilization $USE"
    fi
    ## Mem
    TOTAL=$(free -m | awk '/Mem/{print $2}')
    USE=$(free -m | awk '/Mem/{print $3}')
    FREE=$(free -m | awk '/Mem/{print $4+$6}')
    if [ $FREE -lt 1024 ]; then
        echo "
            Date:$DATE
            Host:$IP
            Problem:Total=$TOTAL,Use=$USE,Free=$FREE"
    fi
    ## disk
    PART_USE=$(df -h | awk -F'[%]+''BEGIN{OFS="="} /^\/dev/{print $1,$2,$5,$6}')
    for i in $PART_USE;
    do
        PART=$(echo $i | cut -d"=" -f1)
        TOTAL=$(echo $i | cut -d"=" -f2)
        USE=$(echo $i | cut -d"=" -f3)
        MOUNT=$(echo $i | cut -d"=" -f4)
        if [ $USE -gt 80]; then
            echo "
            Date:$DATE
            Host:$IP
            Total:$TOTAL
            Problem:$PART=$USE($MOUNT)"
        fi
    done
```
## 8、批量主机磁盘利用率监控
```shell
    #!/bin/bash
    HOST_INFO=host.info
    for IP in $(awk '/^[^#]/ {print $1}' $HOST_INFO); 
    do 
        USER=$(awk -v ip=$IP 'ip==$1 {print $2}' $HOST_INFO)
        PORT=$(awk -v ip=$IP 'ip==$1 {print $3}' $HOST_INFO)
        TMP_FILE=/tmp/disk.tmp
        ssh -p $PORT $USER@$IP df -h > $TMP_FILE
        USE_RATE_LIST=$(awk 'BEGIN{OFS="="}/^\/dev/ {print $NF,int($5)}' $TMP_FILE)
        for USE_RATE in $USE_RATE_LIST;
        do 
            PART_NAME=${USE_RATE%=*}
        ## 从右到左，非贪婪匹配，匹配到的删除
            USE_RATE=${USE_RATE#*=}
        ## 从左到右，非贪婪匹配，匹配到的删除
            if [ $USE_RATE -ge 10]; then
                echo "Warning:$IP $PART_NAME Partition usage $USE_RATE%!"
            fi
        done
    done
```
## 9、检查网站可用性
* 1） 检查`URL`可用性
```shell
    #!/bin/bash
    #-------------
    ## 方法1
    check_url(){
        HTTP_CODE=$(curl-o /dev/null --connect-timeout 3 -s -w "%{http_code}" $1)
        if [ $HTTP_CODE -ne 200]; then
                echo "Warning:$1 Access failure!"
        fi
    }
    ## 方法2
    check_url2(){
        if ! wget -T 10 --tries=1 --spider $1 &> /dev/null; then
            echo "Warning:$1 Access failure!"
        fi
    }
    check_url www.baidu.com
    check_url2 www.baidu.com        
```
* 2)判断三次`URL`可用性
```shell
    #思路与上面检查主机存活状态一样。
    #-----------------------------
    URL_LIST="www.baidu.com www.agasgf.com"
    #------------------------------
    #方法1：利用循环技巧，如果成功就跳出当前循环，否则执行到最后一行
    check_url3(){
        HTTP_CODE=$(curl -o /dev/null --connect-timeout 3 -s -w "%{http_code}" $1)
        if [ $HTTP_CODE -eq 200 ]; then
            continue
        fi
    }
    for URL in $URL_LIST;
    do
        check_url3 $URL
        check_url3 $URL
        check_url3 $URL
        echo "Warning:$URL Access failure!"
    done
    #--------------------------------
    #方法2：错误次数保存到变量
    for URL in $URL_LIST;
    do
        FAIL_COUNT=0
        for ((i=1;i<=3;i++));
        do
            HTTP_CODE=$(curl -o /dev/null --connect-timeout 3 -s -w "%{http_code}" $URL)
            if [ $HTTP_CODE -ne 200 ]; then
                let FAIL_COUNT++
            else
                break
            fi
        done
        if [ $FAIL_COUNT -eq 3 ]; then
            echo "Warning:$URL Access failure!"
        fi
    done
    #----------------------------------
    #方法3：错误次数保存到数组
    for URL in $URL_LIST;
    do 
        NUM=1
        unset FAIL_COUNT
        while [ $NUM -le 3 ];
        do
            HTTP_CODE=$(curl -o /dev/null -connect-timeout 3 -s -w "%{http_code}" $URL)
            if [ $HTTP_CODE -ne 200 ]; then
                FAIL_COUNT[$NUM]=$URL
                let NUM++
            else
                break
            fi
        done
        if [ ${#FAIL_COUNT[@]} -eq 3 ]; then
            echo "Warning:$URL Access failure!"
        fi
    done
```
## 10、检查`MySQL`主从同步状态
```shell
    #!/bin/bash
    USER=bak
    PASSWD=123456
    IO_SQL_STATUS=$(mysql -u$USER -p$PASSWD -e show slave statusG|awk -F: /Slave_.*_Running/{gsub(":",":");print $0})
    for i in $IO_SQL_STATUS;
    do
        THREAD_STATUS_NAME=${i%:*}
        THREAD_STATUS=${i#*:}
        if [ "$THREAD_STATUS"!="Yes"]; then
            echo "Error:MySQL Master-Slave $THREAD_STATUS_NAME status is $THREAD_STATUS!"
        fi
    done
```