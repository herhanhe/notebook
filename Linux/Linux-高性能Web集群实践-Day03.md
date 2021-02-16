Linux-高性能Web集群实践
===

# Day03. Rsync数据复制软件应用实践

## 1.Rsync介绍
* Rsyn是一款开源的快速的、可实现全量及增量的本地或远程数据备份的多功能优秀工具。
  * 在复制时可以不改变原有数据的属性信息
  * 可实现数据的备份迁移特性
  * 适用于Unix/Linux/Windows等多种操作系统平台
  * 它以其delta-transfer算法闻名
  * 通过减少网络数据发送数量，只发送源文件和目标文件之间的差异信息，从而实现数据的增量复制
  * 官网链接：http://www.samba.org/ftp/rsync/rsync.html
  * 官方手册：`man rsync`/`man rsync.conf`

### 1.1 Rsync功能
* 1）实现本地数据同步复制（本地工作模式，相当于`cp`命令）。
* 2）实现远程数据同步复制（远程shell工作模式，相当于`scp`命令）。
* 3）实现数据信息删除功能（本地工作模式，相当于`rm`命令）。
* 4）实现数据信息查看功能（本地或远程工作模式，相当于`ls`命令）。

### 1.2 Rsync软件特性
* 支持拷贝普通文件与特殊文件，如链接文件、设备文件等。
* 支持排除指定文件或目录同步的功能，相当于打包命令`tar`的排除功能。
* 可以做到保持原文件或目录的权限、时间、软硬链接、属主、组等所有属性均不改变。
* 可实现增量复制，即只复制发生变化的数据，因此数据传输效率很高。
* 可以使用`rcp`、`rsh`、`SSH`等方式来配合进行隧道加密传输文件（Rsync本身不对数据加密）
* 可以通过scoket（进程方式）传输文件和数据（服务端和客户端）。
* 支持匿名或认证（无须系统用户）进程模式传输，安全地进行数据备份及镜像。

### 1.3 Rsync复制原理
![3-1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/web/3-1.png)
* 1.连接两台服务器确认两台服务器之间可以正常进行数据传递
* 2.利用算法监测目标主机与当前主机的数据信息请求
* 3.确认无误后将增量变化的数据进行传递（默认情况）
* 底层的代码层算法原理，可以参考 https://coolshell.cn/articles/7425.html 中的相关内容。

## 2.Rsync工作方式介绍与实践

* 1.**本地（Local）数据传输模式**
  * Rsync的本地数据传输模式，类似于cp本地复制命令，可以实现文件、目录的移动备份等功能
  * 不同的是Rsync有增量复制的功能
* 2.**远程Shell数据传输模式**
  * 远程Shell数据传输模式一般是借助通道（如SSH）在两台服务器之间进行复制数据，这两台服务器是对等的，没有客户端与服务端之分。
  * 类似于scp远程复制命令，但缺少scp的加密复制的功能
  * 不同的是Rsync有增量复制的功能
* 3.**守护进程（Daemon）传输模式**
  * 守护进程传输模式是在客户端与服务端之间进行数据复制的，通常需要服务端部署守护进程服务，然后在客户端执行命令，实现数据的拉取和推送复制。

### 2.1本地数据传输模式

* 1.语法
  rsyn|[OPTION...]|SRC...|[DEST]
  -|-|-|-
  rsync命令|参数选项|[源目录或文件]|目的目录或文件
* 2.实例
  * （1）作为本地复制命令应用（类似cp命令）
    * 利用rsync命令实现本地文件的复制
    ```shell
      [root@backup ~]# cd /tmp
      [root@backup tmp]# ls
      [root@backup tmp]# rsync /etc/hosts /tmp/       #<== 实现hosts文件本地复制。
      [root@backup tmp]# ls -l
      总用量 4
      -rw-r--r-- 1 root root 346 2月  16 16:11 hosts
      # 说明：根据以上操作信息，实现了本地拷贝文件操作
    ```
    * 利用rsync命令实现复制本地目录
    ```shell
      [root@backup tmp]# mkdir /oldboy_dir -p                     #<==创建测试目录
      [root@backup tmp]# touch /oldboy_dir/file{1..5}.txt         #<==在测试目录中，创建5个测试文件
      [root@backup tmp]# ls -l
      总用量 4
      -rw-r--r-- 1 root root 346 2月  16 16:11 hosts
      [root@backup tmp]# ls /oldboy_dir/
      file1.txt  file2.txt  file3.txt  file4.txt  file5.txt
      [root@backup tmp]# rsync /oldboy_dir  /tmp/                 #<==直接复制目录会显示报错信息
      skipping directory oldboy_dir
      [root@backup tmp]# rsync -r /oldboy_dir  /tmp/              #<==复制目录需要加入-r参数
      [root@backup tmp]# ls 
      hosts  oldboy_dir                                           #<==本地目录复制成功
      [root@backup tmp]# ls  oldboy_dir/      
      file1.txt  file2.txt  file3.txt  file4.txt  file5.txt       #<==测试目录中数据也复制成功
      # 说明：根据以上操作信息，实现了本地拷贝目录操作
    ```
  * （2）作为删除数据命令应用（类似rm命令）
    * 利用rsync命令的删除功能清空文件夹内容
    ```shell
      [root@backup tmp]# mkdir /opt/null -p                       #<==创建一个空目录
      [root@backup tmp]# rsync -r --delete /opt/null/ /tmp/       #<==清除/tmp下所有内容
      [root@backup tmp]# ls -l                                    #<==已删除
      总用量 0
      # 说明：利用上面所学的本地复制命令结合删除功能参数--delete，对tmp目录进行清除
      # 提示：在使用--delete删除功能参数时，必须结合-r或-d参数使用
    ```
    * 利用rsync命令的删除功能清空文件内容
    ```shell
      [root@backup tmp]# echo "www.oldboyedu.com" > file1.txt     #<==生成测试文件
      [root@backup tmp]# cat file1.txt 
      www.oldboyedu.com
      [root@backup tmp]# touch null.txt
      #<==创建空文件null.txt，利用空文件实现对相应file1.txt文件内容的清除
      [root@backup tmp]# rsync -r --delete /tmp/null.txt file1.txt
      #<==利用rsync命令删除功能实现对文件数据信息清除
      [root@backup tmp]# cat file1.txt                            #<==数据信息已清除
    ```
    * 提示：在使用rsync命令对目录数据进行本地或远程复制时，目录名称后面不加“/”,会将/oldboy_dir目录及目录下面的文件内容一并复制到tmp目录中；目录后面加“/”，会将/oldboy_dir目录下面的文件内容复制到tmp目录中，但oldboy_dir目录本身没有被复制。
  * （3）作为查询数据命令应用（类似ls命令）
    * 利用rsync命令的查询功能查看文件和目录信息
    ```shell
      [root@backup tmp]# rsync /etc/hosts                         #<==查询文件信息
      -rw-r--r--            346 2021/01/11 00:40:34 hosts
      [root@backup tmp]# rsync /etc
      drwxr-xr-x          8,192 2021/02/17 00:05:06 etc           #<==查询目录信息
    ```
