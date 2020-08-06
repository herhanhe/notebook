<!--
 * @Author: herhan
 * @Date: 2020-06-30 22:51:03
 * @LastEditTime: 2020-07-14 22:17:17
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \github\notebook\Linux\Linux-day17.md
-->

# `Linux` 编程环境 -- `Vim`编辑器

## 1.`Vim`简介

* `Vi`是`visual editor`的简写，发音为[`vi'ai`],是`UNIX`系统下最通用的文本编辑器。`Vi`不是一个所见即所得的编辑器，如果要进行复制和格式化文本需要手动输入命令进行操作。安装好Linux操作系统后，一般已经默认安装好`Linux`操作系统后，一般已经默认安装了`Vi`编译器。为了使用方便，建议安装`Vi`的扩展版本`Vim`，它比`Vi`更强大。

### 1.1 `Vim`的安装

* yum库安装
  
    ```shell
        #Ubuntu
        [root@localhost ~]# apt-get install vim

        #REHL&Centos
        [root@localhost ~]# yum install vim
    ```

* 源码编译安装

  * 1.彻底卸载操作系统自带的 `vim`

    ```shell
        #Ubuntu
        [root@localhost ~]# sudo apt-get remove --purge vi vim-tiny vim vim-runtime gvim vim-common vim-gui-common vim-nox

        #REHL&Centos
        [root@localhost ~]# rpm -qa|grep vim
        vim-minimal-7.2.411-1.8.el6.x86_64
        vim-enhanced-7.2.411-1.8.el6.x86_64
        vim-common-7.2.411-1.8.el6.x86_64
        [root@localhost ~]# rpm -e vim-minimal-7.2.411-1.8.el6.x86_64 vim-enhanced-7.2.411-1.8.el6.x86_64 vim-common-7.2.411-1.8.el6.x86_64 --nodeps
    ```

  * 2.克隆 `vim` 源代码

    ```shell
        [root@localhost ~]# git clone https://github.com/vim/vim.git
        [root@localhost ~]# cd ./vim
    ```
  
  * 3.安装依赖库

    ```shell
        #Ubuntu
        [root@localhost ~]# sudo apt install libncurses5-dev libgnome2-dev libgnomeui-dev libgtk2.0-dev libatk1.0-dev libbonoboui2-dev libcairo2-dev libx11-dev libxpm-dev libxt-dev python-dev python3-dev ruby-dev lua5.1 liblua5.1-dev libperl-dev git

        #REHL&Centos
        [root@localhost ~]# sudo yum install -y ruby ruby-devel lua lua-devel luajit luajit-devel ctags git python python-devel python3 python3-devel tcl-devel perl perl-devel perl-ExtUtils-ParseXS perl-ExtUtils-XSpp perl-ExtUtils-CBuilder perl-ExtUtils-Embed
        [root@localhost ~]# sudo ln -s /usr/bin/xsubpp /usr/share/perl5/ExtUtils/xsubpp
    ```

  * 4.配置 `Vim`

    ```shell
        [root@localhost ~]# ./configure --with-features=huge \
                            --enable-multibyte \
                            --enable-rubyinterp=yes \
                            --enable-python3interp=yes \
                            --with-python-config-dir=/usr/lib/python3.7/config-3.7m-x86_64-linux-gnu \
                            --enable-perlinterp=yes \
                            --enable-luainterp=yes \
                            --enable-gui=gtk2 \
                            --enable-cscope \
                            --prefix=/usr/local
    ```

  * 5.编译安装

    ```shell
        [root@localhost ~]# make VIMRUNTIMEDIR=/usr/local/share/vim/vim81
        [root@localhost ~]# sudo make install
        [root@localhost ~]# vim --version
    ```

### 1.2 `Vim`编译器的模式

* `Vim`主要分为普通模式和插入模式。普通模式是命令模式，插入模式是编辑模式。
  * 插入模式下可以进行字符的输入，输入的键值显示在编辑框中，这些文本可以用于编辑。
  * 普通模式是进行命令操作的，输入的值代表一个命令。
  * 插入模式和普通模式的切换分别为按`i`键和`ESC`键。在普通模式下按`i`键，会转入插入模式；在插入模式下按`ESC`键进入普通模式。
  * 在用户进入`Vim`还没有进行其他操作时，默认是普通模式

![12-1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/20-1.png)

## 2.