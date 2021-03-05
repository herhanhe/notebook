Python_edu_csdn
===

# Day01 Python语言家族
## 1. 认识Python语言
### 1.1 Python是什么
* Python是什么？
  * Python是一门编程语言，帮助我们更好的与计算机沟通
* Python的使命？
  * 功能权全面，易学易用，可拓展的语言
### 1.2 Python背景介绍
* Python的作者是荷兰人Guido van Rossum（龟叔）
* Python正式诞生于1991年
* Python目前有两个版本，Python2和Python3，代码不完全兼容
* 源文件.py文件名后缀
* Python的解释器如今有多个语言实现，我们常用的是Cpython或者IPython（官网版本的C语言实现），其他还有Jython（Java平台）、IronPython（NET和Mono平台）、PyPy（Python实现）
### 1.3 Python语言特点
* Python是一门跨平台，开源语言
* 解释型高级语言
  * 高级语言 
    ![1-1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/python_edu/1-1.png)
  * 编译型原理
    * 代表语言：C语言，C++语言
    ![1-2](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/python_edu/1-2.png)
    * 总结：编译器将源代码逐条编译成机器代码，任何一条语句有错，都不能通过编译，最终形成目标代码，以后每次执行只执行目标代码即可，因此执行速度快。
  * 解释型原理
    * 代表语言：Python语言，JavaScript语言
    ![1-3](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/python_edu/1-3.png)
    * 总结：解释器逐条解释源代码，成功一条就执行一条，每次执行时都需要解释，所以速度稍慢。
* Python开发效率高，执行效率低
* 源代码可见
* 开发代码量少
* 模块扩展库种类繁多
### 1.4 Python可以做什么
* 爬虫
* Web后台开发
* 自动化运维
* 自动化测试
* 人工智能
* 数据科学

## 2. Hello Python
### 2.1 Python环境安装
* 2.1.1 Python环境包含：
  * Python解释器（CPython）
    * 解释执行Python代码
  * 编辑器（IDLE，PyCharm，Sublime Text）
    * 编写Python代码的工具
  * Python软件包管理系统（pip）
    * 从网上下载别人已经写好的程序，来为你工作
* 2.1.2 Python环境下载：
  * Python最新源码，解释器环境等最新下载
    * Python官网：http://www.python.org/
  * Python权威使用技术文档等最新下载
    * Python文档下载地址：http://www.python.org/doc/
* 2.1.3 Python编辑器
  * Ipython IDLE
  * Vim和macVim（linux，unix，macOSX）
  * Eclipse+pyDEV
  * Xcode（macOSX）
  * Notepad++（win）
  * Sublime Text（win，macOSX，linux，unix）
  * PyCharm（全平台）
* 2.1.4 Python环境安装
  * 通过ubuntu官方的apt工具包安装
    ```shell
      $ sudo apt-get install python
      $ sudo apt-get install python2.7
      $ sudo apt-get install python3.7
    ```
  * 苹果Mac OSX通过homebrew工具包安装
    ```shell
      $ brew sreach python
      $ brew install python3
    ```
    * 安装在/usr/local/Cellar/这个目录下
  * Windows下直接下载安装
    * 首先访问http://www.python.org/download/下载最新环境
      ![1-4](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/python_edu/1-4.png)
    * 然后选择最新版本，并适用于自己系统的环境
    * 下载后，双击安装，选择“Install Now”默认安装，但一定“Add Python 3.7 to PATH”  
### 2.2 运行Python代码