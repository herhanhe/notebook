---
title:  
tags: 学习
notebook: shell
---
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
            if ping -c 1 $IP 
```