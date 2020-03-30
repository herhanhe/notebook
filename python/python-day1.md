---
title: python-day1-python基础1
tags: 
notebook: pyhton
---

# Python基础1

---

<!-- TOC -->

- [Python基础1](#python基础1)
    - [1.Python介绍](#1python介绍)
        - [Python是一门什么样的语言?](#python是一门什么样的语言)
        - [Python的优缺点](#python的优缺点)
        - [Python解释器](#python解释器)
    - [2.Python的发展史](#2python的发展史)
    - [3.Python安装](#3python安装)
    - [4.Hello World程序](#4hello-world程序)
    - [5.变量](#5变量)
    - [6.字符编码](#6字符编码)
        - [注释](#注释)
    - [7.用户输入](#7用户输入)
    - [8.格式化输出](#8格式化输出)
    - [9.模块初识](#9模块初识)
    - [10. `.pyc`是个什么鬼？](#10-pyc是个什么鬼)
    - [11.数据类型初始](#11数据类型初始)
    - [12.数据运算](#12数据运算)
    - [13.表达式`if ... else`](#13表达式if--else)
    - [14.表达式for loop](#14表达式for-loop)
    - [15.while loop](#15while-loop)
    - [16.练习](#16练习)

<!-- /TOC -->

## 1.Python介绍

`python`的创始人为吉多·范罗苏姆（`Guido van Rossum`）。1989年的圣诞节期间，吉多·范罗苏姆为了在阿姆斯特丹打发时间，决心开发一个新的脚本解释程序，作为`ABC`语言的一种继承。

* **目前Python主要应用领域：**
  * **云计算:** 云计算最火的语言， 典型应用`OpenStack`
  * **`WEB`开发**: 众多优秀的`WEB`框架，众多大型网站均为`Python`开发，`Youtube`, `Dropbox`, 豆瓣。。。， 典型`WEB`框架有`Django`
  * **科学运算、人工智能:** 典型库`NumPy`, `SciPy`, `Matplotlib`, `Enthought librarys`,`pandas`
  * **系统运维:** 运维人员必备语言
  * **金融：**量化交易，金融分析，在金融工程领域，`Python`不但在用，且用的最多，而且重要性逐年提高。原因：作为动态语言的`Python`，语言结构清晰简单，库丰富，成熟稳定，科学计算和统计分析都很牛逼，生产效率远远高于`c,c++,java`,尤其擅长策略回测
  * **图形`GUI`**: `PyQT`, `WxPython`,`TkInter`
* **Python在一些公司的应用：**
  * 谷歌：`Google App Engine` 、`code.google.com` 、`Google earth` 、`谷歌爬虫`、`Google广告等项目都在大量使用Python开发`
  * `CIA`: 美国中情局网站就是用`Python`开发的
  * `NASA`: 美国航天局(`NASA`)大量使用`Python`进行数据分析和运算
  * `YouTube`:世界上最大的视频网站`YouTube`就是用`Python`开发的
  * `Dropbox`:美国最大的在线云存储网站，全部用`Python`实现，每天网站处理10亿个文件的上传和下载
  * `Instagram`:美国最大的图片分享社交网站，每天超过3千万张照片被分享，全部用`python`开发
  * `Facebook`:大量的基础库均通过`Python`实现的
  * `Redhat`: 世界上最流行的`Linux`发行版本中的`yum`包管理工具就是用`python`开发的
  * 豆瓣: 公司几乎所有的业务均是通过`Python`开发的
  * 知乎: 国内最大的问答社区，通过`Python`开发(国外`Quora`)
  * 春雨医生：国内知名的在线医疗网站是用`Python`开发的
  * 除上面之外，还有搜狐、金山、腾讯、盛大、网易、百度、阿里、淘宝 、土豆、新浪、果壳等公司都在使用`Python`完成各种各样的任务。

### Python是一门什么样的语言?

---

* **编译和解释的区别是什么？**

  * **编译器**是把源程序的每一条语句都编译成机器语言,并保存成二进制文件,这样运行时计算机可以直接以机器语言来运行此程序,速度很快;
  * **解释器**则是只在执行程序时,才一条一条的解释成机器语言给计算机来执行,所以运行速度是不如编译后的程序运行的快的.
  * 这是因为计算机不能直接认识并执行我们写的语句,它只能认识机器语言(是二进制的形式)

![1](https://pic.downk.cc/item/5e2ea5b32fb38b8c3cac385c.png)
![2](https://pic.downk.cc/item/5e2ea6f92fb38b8c3cac527f.png)

* **编译型vs解释型**
  * **编译型**
    * 优点：编译器一般会有预编译的过程对代码进行优化。因为编译只做一次，运行时不需要编译，所以编译型语言的程序执行效率高。可以脱离语言环境独立运行。
    * 缺点：编译之后如果需要修改就需要整个模块重新编译。编译的时候根据对应的运行环境生成机器码，不同的操作系统之间移植就会有问题，需要根据运行的操作系统环境编译不同的可执行文件。

  * **解释型**
    * 优点：有良好的平台兼容性，在任何环境中都可以运行，前提是安装了解释器（虚拟机）。灵活，修改代码的时候直接修改就可以，可以快速部署，不用停机维护。
    * 缺点：每次运行的时候都要解释一遍，性能上不如编译型语言。

* **动态语言和静态语言**
  * **动态类型语言**：动态类型语言是指在运行期间才去做数据类型检查的语言，也就是说，在用动态类型的语言编程时，永远也不用给任何变量指定数据类型，该语言会在你第一次赋值给变量时，在内部将数据类型记录下来。`Python`和`Ruby`就是一种典型的动态类型语言，其他的各种脚本语言如`VBScript`也多少属于动态类型语言。

  * **静态类型语言**：静态类型语言与动态类型语言刚好相反，它的数据类型是在编译其间检查的，也就是说在写程序时要声明所有变量的数据类型，`C/C++`是静态类型语言的典型代表，其他的静态类型语言还有`C#、JAV`A等。

* **强类型定义语言和弱类型定义语言**
  * **强类型定义语言**：强制数据类型定义的语言。也就是说，一旦一个变量被指定了某个数据类型，如果不经过强制转换，那么它就永远是这个数据类型了。举个例子：如果你定义了一个整型变量a,那么程序根本不可能将`a`当作字符串类型处理。强类型定义语言是类型安全的语言。
  * **弱类型定义语言**：数据类型可以被忽略的语言。它与强类型定义语言相反, 一个变量可以赋不同数据类型的值。
  * 强类型定义语言在速度上可能略逊色于弱类型定义语言，但是强类型定义语言带来的严谨性能够有效的避免许多错误。另外，“这门语言是不是动态语言”与“这门语言是否类型安全”之间是完全没有联系的！
* `python`是一门动态解释性的强类型定义语言

### Python的优缺点

---

* 先看优点
  1. `Python`的定位是“优雅”、“明确”、“简单”，所以`Python`程序看上去总是简单易懂，初学者学`Python`，不但入门容易，而且将来深入下去，可以编写那些非常非常复杂的程序。
  2. 开发效率非常高，`Python`有非常强大的第三方库，基本上你想通过计算机实现任何功能，`Python`官方库里都有相应的模块进行支持，直接下载调用后，在基础库的基础上再进行开发，大大降低开发周期，避免重复造轮子。
  3. 高级语言————当你用`Python`语言编写程序的时候，你无需考虑诸如如何管理你的程序使用的内存一类的底层细节
  4. 可移植性————由于它的开源本质，`Python`已经被移植在许多平台上（经过改动使它能够工 作在不同平台上）。如果你小心地避免使用依赖于系统的特性，那么你的所有`Python`程序无需修改就几乎可以在市场上所有的系统平台上运行
  5. 可扩展性————如果你需要你的一段关键代码运行得更快或者希望某些算法不公开，你可以把你的部分程序用C或C++编写，然后在你的Python程序中使用它们。
  6. 可嵌入性————你可以把Python嵌入你的C/C++程序，从而向你的程序用户提供脚本功能。
* 再看缺点：
  1. 速度慢，`Python`的运行速度相比`C`语言确实慢很多，跟`JAVA`相比也要慢一些，因此这也是很多所谓的大牛不屑于使用`Python`的主要原因，但其实这里所指的运行速度慢在大多数情况下用户是无法直接感知到的，必须借助测试工具才能体现出来，比如你用`C`运一个程序花了0.01s,用`Python`是0.1s,这样`C`语言直接比`Python`快了10倍,算是非常夸张了，但是你是无法直接通过肉眼感知的，因为一个正常人所能感知的时间最小单位是0.15-0.4s左右，哈哈。其实在大多数情况下`Python`已经完全可以满足你对程序速度的要求，除非你要写对速度要求极高的搜索引擎等，这种情况下，当然还是建议你用`C`去实现的。
  2. 代码不能加密，因为`PYTHON`是解释性语言，它的源码都是以名文形式存放的，不过我不认为这算是一个缺点，如果你的项目要求源代码必须是加密的，那你一开始就不应该用`Python`来去实现。
  3. 线程不能利用多`CPU`问题，这是`Python`被人诟病最多的一个缺点，`GIL`即全局解释器锁（`Global Interpreter Lock`），是计算机程序设计语言解释器用于同步线程的工具，使得任何时刻仅有一个线程在执行，`Python`的线程是操作系统的原生线程。在`Linux`上为`pthread`，在`Windows`上为`Win thread`，完全由操作系统调度线程的执行。一个`python`解释器进程内有一条主线程，以及多条用户程序的执行线程。即使在多核`CPU`平台上，由于`GIL`的存在，所以禁止多线程的并行执行。关于这个问题的折衷解决方法，我们在以后线程和进程章节里再进行详细探讨。

### Python解释器

---

* **`CPython`**
  * 当我们从`Python`官方网站下载并安装好`Python 2.7`后，我们就直接获得了一个官方版本的解释器：`CPython`。这个解释器是用`C`语言开发的，所以叫`CPython`。在命令行下运行`python`就是启动`CPython`解释器。

  * `CPython`是使用最广的`Python`解释器。教程的所有代码也都在`CPython`下执行。

* **IPython**
  * `IPython`是基于`CPython`之上的一个交互式解释器，也就是说，`IPython`只是在交互方式上有所增强，但是执行`Python`代码的功能和`CPython`是完全一样的。好比很多国产浏览器虽然外观不同，但内核其实都是调用了IE。
  * `CPython`用`>>>`作为提示符，而`IPython`用`In [序号]:`作为提示符。

* **PyPy**
  * `PyPy`是另一个`Python`解释器，它的目标是执行速度。`PyPy`采用`JIT`技术，对`Python`代码进行动态编译（注意不是解释），所以可以显著提高`Python`代码的执行速度。
  * 绝大部分Python代码都可以在PyPy下运行，但是PyPy和CPython有一些是不同的，这就导致相同的Python代码在两种解释器下执行可能会有不同的结果。如果你的代码要放到PyPy下执行，就需要了解PyPy和CPython的不同点。

* **Jython**
  * `Jython`是运行在`Java`平台上的`Python`解释器，可以直接把`Python`代码编译成`Java`字节码执行。

* **IronPython**
  * `IronPython`和`Jython`类似，只不过`IronPython`是运行在微软`.Net`平台上的`Python`解释器，可以直接把`Python`代码编译成`.Net`的字节码。

## 2.Python的发展史

* 1989年，为了打发圣诞节假期，`Guido`开始写`Python`语言的编译器。`Python`这个名字，来自`Guido`所挚爱的电视剧`Monty Python’s Flying Circus`。他希望这个新的叫做`Python`的语言，能符合他的理想：创造一种`C`和`shell`之间，功能全面，易学易用，可拓展的语言。
* 1991年，第一个`Python`编译器诞生。它是用`C`语言实现的，并能够调用`C`语言的库文件。从一出生，`Python`已经具有了：类，函数，异常处理，包含表和词典在内的核心数据类型，以及模块为基础的拓展系统。
* `Granddaddy of Python web frameworks, Zope 1 was released in 1999`
* `Python 1.0 - January 1994` 增加了 `lambda, map, filter and reduce`.
* `Python 2.0 - October 16, 2000`，加入了内存回收机制，构成了现在`Python`语言框架的基础
* `Python 2.4 - November 30, 2004`, 同年目前最流行的`WEB`框架`Django` 诞生
* `Python 2.5 - September 19, 2006`
* `Python 2.6 - October 1, 2008`
* `Python 2.7 - July 3, 2010`
* `In November 2014, it was announced that Python 2.7 would be supported until 2020, and reaffirmed that there would be no 2.8 release as users were expected to move to Python 3.4+ as soon as possible`
* `Python 3.0 - December 3, 2008`
* `Python 3.1 - June 27, 2009`
* `Python 3.2 - February 20, 2011`
* `Python 3.3 - September 29, 2012`
* `Python 3.4 - March 16, 2014`
* `Python 3.5 - September 13, 2015`

## 3.Python安装

* `windows`
  * 下载安装包
  * 安装
  * 配置环境变量
* `linux,mac`
  * 无需安装，原装Python环境

## 4.Hello World程序

* **指定解释器**
  * 上一步中执行 `python hello.py` 时，明确的指出 `hello.py` 脚本由 `python` 解释器来执行。

```python
    #!/usr/bin/env python   //在 hello.py 文件的头部指定解释器
  
    print "hello,world"
```

* **在交互器中执行**
  * 除了把程序写在文件里，还可以直接调用python自带的交互器运行代码

```python
    localhost:~ jieli$ python
    Python 2.7.10 (default, Oct 23 2015, 18:05:06)
    [GCC 4.2.1 Compatible Apple LLVM 7.0.0 (clang-700.0.59.5)] on darwin
    Type "help", "copyright", "credits" or "license" for more information.
    >>> print("Hello World!")
    Hello World!
```

* 对比下其它语言的`hello World`程序

  * C++
  
    ```c++
        #include <iostream>
        int main(void)
        {
            std::cout<<"Hello world";
        }
    ```

  * C

    ```c
        #include <stdio.h>
        int main(void)
        {
            printf("\nhello world!");
            return 0;
        }
    ```

  * JAVA

    ```java
        public class HelloWorld{
            // 程序的入口
            public static void main(String args[]){
                // 向控制台输出信息
               System.out.println("Hello World!");
           }
        }
    ```

  * PHP

    ```php
        <?php  
             echo "hello world!";  
        ?>
    ```

  * RUBY

    ```ruby
        puts "Hello world."
    ```

  * GO

    ```go
        package main

        import "fmt"

        func main(){

            fmt.Printf("Hello World!\n God Bless You!");

        }
    ```

## 5.变量

* **Variables** are used to store information to be referenced and manipulated in a computer program. They also provide a way of labeling data with a descriptive name, so our programs can be understood more clearly by the reader and ourselves. It is helpful to think of variables as containers that hold information. Their sole purpose is to label and store data in memory. This data can then be used throughout your program.

* **变量声明**
  
  ```python
    #_*_coding:utf-8_*_

    name = "Alex Li"
  ```

* **变量定义的规则**
  * 变量名只能是 字母、数字或下划线的任意组合
  * 变量名的第一个字符不能是数字
  * 以下关键字不能声明为变量名
  
  ```python
    ['and','as','assert','break','class','continue','def','del'.'elif','else','except','exec','finally','for','from','global','if','import','in','is','lambda','not','or','pass','print','raise','return','try','while','with','yield']
  ```

* **变量的赋值**
  
  ```python
    name = "Alex Li"

    name2 = name
    print(name,name2)

    name = "Jack"

    print("What is the value of name2 now?")
  ```

## 6.字符编码

* `python`解释器在加载`.py`中的代码时，会对内容进行编码（默认`ascll`）
* `ASCLL(American Standard Code for Information Interchange, 美国标准信息交换代码)`是基于拉丁字母的一套电脑编码系统，主要用于显示现代英语和其他西欧语言，其最多只用8位来表示（一个字节），即：2**8 = 256 - 1,所以，`ASCLL`码最多只能表示255个符号。
![3](https://pic.downk.cc/item/5e3bd9872fb38b8c3ce669cc.png)

* **关于中文**
  * 为了处理汉字，程序员设计了用于简体中文的**GB2312**和用于繁体中文的`big5`。
  * **GB2312**(1980年)一共收录了7445个字符，包括6763个汉字和682个其他符号。汉字区的内码范围高字节从`B0-F7`,低字节从`A1-FE`,占用的码位是72*94=6768。其中有5个空位是`D7FA-D7FE`。
  * **GB2312** 支持的汉字太少。1995年的汉字扩展规范**GBK1.0**收录了21886个符号，它分为汉字区和图形符号区。汉字区包括21003个字符。2000年的 **GB18030**是取代**GBK1.0**的正式国家标准。该标准收录了27484个汉字，同时还收录了藏文、蒙文、维吾尔文等主要的少数民族文字。现在的PC平台必须支持**GB18030**，对嵌入式产品暂不作要求。所以手机、MP3一般只支持**GB2312**。
  * 从**ASCII**、**GB2312**、**GBK** 到**GB18030**，这些编码方法是向下兼容的，即同一个字符在这些方案中总是有相同的编码，后面的标准支持更多的字符。在这些编码中，英文和中文可以统一地处理。区分中文编码的方法是高字节的最高位不为0。按照程序员的称呼，**GB2312**、**GBK**到**GB18030**都属于双字节字符集 (**DBCS**)。

  * 有的中文`Windows`的缺省内码还是**GBK**，可以通过**GB18030**升级包升级到**GB18030**。不过**GB18030**相对**GBK**增加的字符，普通人是很难用到的，通常我们还是用**GBK**指代中文`Windows`内码。

* 显然**ASCII**码无法将世界上的各种文字和符号全部表示，所以，就需要新出一种可以代表所有字符和符号的编码，即：**Unicode**
  * **Unicode**（统一码、万国码、单一码）是一种在计算机上使用的字符编码。**Unicode** 是为了解决传统的字符编码方案的局限而产生的，它为每种语言中的每个字符设定了统一并且唯一的二进制编码，规定虽有的字符和符号最少由 16 位来表示（2个字节），即：2 **16 = 65536，
注：此处说的的是最少2个字节，可能更多

  * **UTF-8**，是对**Unicode**编码的压缩和优化，他不再使用最少使用2个字节，而是将所有的字符和符号进行分类：**ascii**码中的内容用1个字节保存、欧洲的字符用2个字节保存，东亚的字符用3个字节保存...

* 所以，`python`解释器在加载`.py`文件中的代码时，会对内容进行编码（默认`ascill`），如果是如下代码的话：
  报错：`ascii`码无法表示中文
  
  ```python
    #!/usr/bin/env python

    print "你好，世界"
  ```
  
  改正：应该显示的告诉`python`解释器，用什么编码来执行源代码，即：
  
  ```python
    #!/usr/bin/env python
    # -*- coding:utf-8 -*-

    print "你好，世界"
  ```

### 注释

---

* 当行注释： `#` 被注释内容
* 多行注释： """ 被注释内容 """

## 7.用户输入

```python
    #!/usr/bin/env python
    #_*_coding:utf-8_*_

    #name = raw_input("What is your name?") #only on python 2.x
    name = input("What is your name?")
    print("Hello" + name)
```

* 输入密码时，如果想要不可见，需要利用`getpass`模块中的`getpass`方法，即：

```python
    #!/usr/bin/env python
    #-*- coding:utf-8 -*-

    import getpass

    # 将用户输入的内容赋值给 name 变量
    pwd = getpass.getpass("请输入密码：")

    # 打印输入的内容
    print（pwd）
```

## 8.格式化输出
> 格式化输出:
> ------- info of herhan --------
> Name:herhan
> Age:12
> Job:it
> Salary:567

```python
  #!/usr/bin/env python
  # -*-coding:utf-8 -*-
  # Author:herhan

  name = input("name :")
  age = int(input("age :"))
  print(type(age))
  job = input("job :")
  salary = input("salary :")
  
  # 方式1
  msg = '''
  ------- info of %s --------
  Name:%s
  Age:%d
  Job:%s
  Salary:%s
  ''' % (name,name,age,job,salary)
  
  # 方式2
  msg1 = '''
  ------- info of {_name} --------
  Name:{_name}
  Age:{_age}
  Job:{_job}
  Salary:{_salary}
  '''.format(
     _name = name,
     _age = age,
     _job = job,
     _salary = salary)
  # 方式3
  msg2 = '''
  ------- info of {0} --------
  Name:{0}
  Age:{1}
  Job:{2}
  Salary:{3}
  '''.format(name,age,job,salary)

  print(msg2)
```

## 9.模块初识

* `python`的强大之外在于他有非常丰富和强大的标准库和第三方库，几乎你想实现的任何功能都有相应的`python`库支持,以后的课程中会深入讲解常用到的各种库，现在，我们先来象征性的学2个简单的

* **sys**

```python
  #!/usr/bin/env pyhton
  # -*- coding: utf-8 -*-

  import sys

  print(sys.path)

  #输出 全局环境变量 模块的路径
  ['E:\\project\\s14\\day01', 'E:\\project\\s14', 
  'D:\\software\\JetBrains\\PyCharm 2019.3.1\\plugins\\python\\helpers\\pycharm_display', 
  'D:\\software\\Python\\Python38\\python38.zip', 
  'D:\\software\\Python\\Python38\\DLLs', 
  'D:\\software\\Python\\Python38\\lib', #标准库
  'D:\\software\\Python\\Python38', 
  'E:\\project\\s14\\venv', 
  'E:\\project\\s14\\venv\\lib\\site-packages', 
  'E:\\project\\s14\\venv\\lib\\site-packages\\setuptools-40.8.0-py3.8.egg', 
  'E:\\project\\s14\\venv\\lib\\site-packages\\pip-19.0.3-py3.8.egg', 
  'D:\\software\\JetBrains\\PyCharm 2019.3.1\\plugins\\python\\helpers\\pycharm_matplotlib_backend']

  print(sys.argv)

  #输出 传递的参数获取 
  $ python test.py hello world
  ['test.py','hello','world']  #把执行脚本时传递的参数获取到了
```

* **os**

```python
  #!/usr/bin/env python
  # -*- coding: utf-8 -*-

  import os

  os.system("df -h") #调用系统命令
  os.mkdir("new_dir")#当前目录创建文件夹
```

* 完全结合一下

```python
  import os,sys

  os.system(''.join(sys.argv[1:])) #把用户的输入的参数当作一条命令交给os.system来执行
```

* **自己写个模板**
  * `python tab`补全模块
    * for mac
  
    ```python
      import sys
      import readline
      import rlcompleter

      if sys.platform == 'darwin' and sys.version_info[0] == 2:
          readline.parse_and_bind("bind ^I rl_complete")
      else:
          readline.parse_and_bind("tab: complete")  # linux and python3 on mac
    ```

    * for linux

    ```python
      #!/usr/bin/env python
      # python startup file
      import sys
      import readline
      import rlcompleter
      import atexit
      import os
      # tab completion
      readline.parse_and_bind('tab: complete')
      # history file
      histfile = os.path.join(os.environ['HOME'], '.pythonhistory')
      try:
          readline.read_history_file(histfile)
      except IOError:
          pass
      atexit.register(readline.write_history_file, histfile)
      del os, histfile, readline, rlcompleter
    ```

* 此时你就要把这个`tab.py`放到`python`全局环境变量目录里啦，基本一般都放在一个叫 `Python/2.7/site-packages` 目录下，这个目录在不同的OS里放的位置不一样，用 `print(sys.path)` 可以查看python环境变量列表.

## 10. `.pyc`是个什么鬼？

1. `Python`是一门解释型语言？
   * 我初学`Python`时，听到的关于`Python`的第一句话就是，`Python`是一门解释性语言，我就这样一直相信下去，直到发现了`*.pyc`文件的存在。如果是解释型语言，那么生成的`*.pyc`文件是什么呢？`c`应该是`compiled`的缩写才对啊！为了防止其他学习`Python`的人也被这句话误解，那么我们就在文中来澄清下这个问题，并且把一些基础概念给理清。

2. 解释型语言和编译型语言
   * 计算机是不能够识别高级语言的，所以当我们运行一个高级语言程序的时候，就需要一个“翻译机”来从事把高级语言转变成计算机能读懂的机器语言的过程。这个过程分成两类，第一种是编译，第二种是解释。
   * 编译型语言在程序执行之前，先会通过编译器对程序执行一个编译的过程，把程序转变成机器语言。运行时就不需要翻译，而直接执行就可以了。最典型的例子就是C语言。
   * 解释型语言就没有这个编译的过程，而是在程序运行的时候，通过解释器对程序逐行作出解释，然后直接运行，最典型的例子是`Ruby`。
   * 通过以上的例子，我们可以来总结一下解释型语言和编译型语言的优缺点，因为编译型语言在程序运行之前就已经对程序做出了“翻译”，所以在运行时就少掉了“翻译”的过程，所以效率比较高。但是我们也不能一概而论，一些解释型语言也可以通过解释器的优化来在对程序做出翻译时对整个程序做出优化，从而在效率上超过编译型语言。
   * 此外，随着`Java`等基于虚拟机的语言的兴起，我们又不能把语言纯粹地分成解释型和编译型这两种。
   * 用`Java`来举例，`Java`首先是通过编译器编译成字节码文件，然后在运行时通过解释器给解释成机器文件。所以我们说`Java`是一种先编译后解释的语言。

3. `Python`到底是什么
   * 其实`Python`和`Java/C#`一样，也是一门基于虚拟机的语言，我们先来从表面上简单地了解一下`Python`程序的运行过程吧。
   * 当我们在命令行中输入`python hello.py`时，其实是激活了`Python`的“解释器”，告诉“解释器”：你要开始工作了。可是在“解释”之前，其实执行的第一项工作和`Java`一样，是编译。
   * 熟悉`Java`的同学可以想一下我们在命令行中如何执行一个`Java`的程序：

    ```
          javac hello.java
          java hello
    ```

   * 只是我们在用`Eclipse`之类的`IDE`时，将这两部给融合成了一部而已。其实`Python`也一样，当我们执行`python hello.py`时，他也一样执行了这么一个过程，所以我们应该这样来描述`Python`，`Python`是一门先编译后解释的语言。

4. 简述`Python`的运行过程
   * 在说这个问题之前，我们先来说两个概念，`PyCodeObject`和`pyc`文件。
   * 我们在硬盘上看到的`pyc`自然不必多说，而其实`PyCodeObject`则是`Python`编译器真正编译成的结果。我们先简单知道就可以了，继续向下看。
   * 当`python`程序运行时，编译的结果则是保存在位于内存中的`PyCodeObject`中，当`Python`程序运行结束时，`Python`解释器则将`PyCodeObject`写回到`pyc`文件中。
   * 当`python`程序第二次运行时，首先程序会在硬盘中寻找`pyc`文件，如果找到，则直接载入，否则就重复上面的过程。
   * 所以我们应该这样来定位`PyCodeObject`和`pyc`文件，我们说`pyc`文件其实是`PyCodeObject`的一种持久化保存方式。

## 11.数据类型初始

1. **数字**
  
    > 2 是一个整数的例子。
    > 长整数 不过是大一些的整数。
    > 3.23和52.3E-4是浮点数的例子。E标记表示10的幂。在这里，52.3E-4表示52.3 * 10-4。
    > (-5+4j)和(2.3-4.6j)是复数的例子，其中-5,4为实数，j为虚数，数学中表示复数是什么？。

   * **int（整型）**
     * 在32位机器上，整数的位数为32位，取值范围为`-2**31～2**31-1`，即-2147483648～2147483647
     * 在64位系统上，整数的位数为64位，取值范围为`-2**63～2**63-1`，即-9223372036854775808～9223372036854775807
  
   * **long（长整型）**
     * 跟`C`语言不同，`Python`的长整数没有指定位宽，即：`Python`没有限制长整数数值的大小，但实际上由于机器内存有限，我们使用的长整数数值不可能无限大。
     * 注意，自从`Python2.2`起，如果整数发生溢出，`Python`会自动将整数数据转换为长整数，所以如今在长整数数据后面不加字母L也不会导致严重后果了。

   * **float（浮点型）**
     * 浮点数用来处理实数，即带有小数的数字。类似于`C`语言中的`double`类型，占8个字节（64位），其中52位表示底，11位表示指数，剩下的一位表示符号。

   * **complex（复数）**
     * 复数由实数部分和虚数部分组成，一般形式为`x＋yj`，其中的`x`是复数的实数部分，`y`是复数的虚数部分，这里的`x`和`y`都是实数。
     * 注：`Python`中存在小数字池：-5 ～ 257

2. **布尔值**

   * 真或假
   * 1 或 0

3. **字符串**
  
      > "hello world"

   * `python`中的字符串在`C`语言中体现为是一个字符数组，每次创建字符串时候需要在内存中开辟一块连续的空，并且一旦需要修改字符串的话，就需要再次开辟空间，万恶的+号每出现一次就会在内从中重新开辟一块空间。

   * **字符串格式化输出**

    ```python
      name = "alex"
      print "I am %s " % name

      #输出： I am alex
    ```

    > PS: 字符串是`%S`;整数`%d`;浮点数`%f`
   * **字符串常用功能**
     * 移除空白
     * 分割
     * 长度
     * 索引
     * 切片
4. **bytes型**
   * `Python3` 新增 `bytes` 类型，是指一堆字节的集合，十六进制表现形式，两个十六进制数构成一个 `byte` ，以 b 开头的字符串都是 `bytes` 类型。
   * 计算机只能存储二进制，字符、图片、视频、音乐等想存到硬盘上，必须以正确的方式编码成二进制后再存，但是转成二进制后不是直接以 0101010 的形式表示的，而是用一种叫 bytes() 的类型来表示
  
    ```python
      str0 = '我是中国人'
      str0.encode(encoding='utf-8')
      #输出
      b'\xe6\x88\x91\xe6\x98\xaf\xe4\xb8\xad\xe5\x9b\xbd\xe4\xba\xba'
    ```

   * **编码&解码**

    ![4](https://pic.downk.cc/item/5e4ba1d448b86553ee50ac99.png)

    ```python
      str0 = '我是中国人'
      # utf-8 编码
      result = str0.encode(encoding = 'utf-8')
      # utf-8 解码
      result1=result.decode(encoding = 'utf-8')
      print(result)
      print(result1)
      #输出
      b'\xe6\x88\x91\xe6\x98\xaf\xe4\xb8\xad\xe5\x9b\xbd\xe4\xba\xba'
      '我是中国人'
    ```

   * 在 `Python3` 中内存里的字符串是以 `Unicode` 编码的，`Unicode` 的其中一个特性就是跟所有语言编码都有映射关系，所以 `UTF-8` 格式的文件，在 `Windows` 电脑上若是不能看，就可以把 `UTF-8` 先解码成 `Unicode` ，再由 `Unicode` 编码成 `GBK` 就可以了。

    ![5](https://pic.downk.cc/item/5e4ba4f448b86553ee5146cc.png)

## 12.数据运算

* 算数运算：

  运算符|描述|实例
  -|-|-
  +|加——两个对象相加|a + b输出结果30
  -|减——得到负数或是一个数减去另一个数|a - b输出结果-10
  `*`|乘——两个数相乘或是返回一个被重复若干次的字符串|a * b输出结果200
  /|除——x除以y|b / a输出结果2
  %|取模——返回除法的余数|b % a输出结果0
  `**`|幂——返回x的y次幂|a ** b为10的2次方，输出结果100
  //|取整除——返回商的整数部分|9 // 2输出结果4，9.0 // 2.0输出结果4.0

* 比较运算：

  运算符|描述|实例
  -|-|-
  ==|等于——比较对象是否相等|（a == b）返回False。
  !=|不等于——比较两个对象是否不相等|（a != b）返回true
  <>|不等于——比较两个对象是否不相等|（a <> b）返回true。这个运算符类似!=。
  `>`|大于——返回x是否大于y|（a > b)返回False。
  <|小于——返回x是否小于y。所有比较运算符返回1表示假。这分别与特殊的变量True和False等价。注意，这些变量名的大写。|（a < b）返回true
  `>=`|大于等于——返回x是否大于等于y。|（a >= b）返回False。
  <=|小于等于——返回x是否小于等于y。|（a <= b）返回true。

* 赋值运算：

  运算符|描述|实例
  -|-|-
  =|简单的赋值运算符|c= a + b将a + b的运算结果赋值为c
  +=|加法赋值运算符|c += a等效于c = c + a
  -=|减法赋值运算符|c -= a等效于c = c - a
  `*=`|乘法赋值运算符|c `*=` a等效于c = c * a
  /=|除法赋值运算符|c /= a等效于c = c / a
  %=|取模赋值运算符|c %= a等效于c = c % a
  `**=`|幂赋值运算符|c `**=` a等效于c = c ** a
  //=|取整除赋值运算符|c //= a等效于c = c // a

* 逻辑运算：

  运算符|描述|实例
  -|-|-
  and|布尔"与"——如果x为False,x and y返回False,否则它返回y的计算值。|（a and b）返回true。
  or|布尔"或"——如果x是True,它返回True,否则它返回y的计算值。|（a or b）返回true。
  not|布尔"非"——如果x为True,返回False,如果x为False,它返回True。|not（a and b）返回false。

* 成员运算：

  运算符|描述|实例
  -|-|-
  in|如果在指定的序列中找到值返回True,否则返回False。|x在y序列中返回True。
  not in|如果在指定的序列中没有找到值返回True,否则返回False。|x不在y序列中，如果x不在y序列中返回True。

* 身份运算：

  运算符|描述|实例
  -|-|-
  is|is是判断两个标识符是不是引用自一个对象|x is y,如果id(x)等于id(y),is返回结果1。
  is not|is not是判断两个标识符是不是引用自不同对象|x is not y,如果id(x)不等于id(y),is not返回值1

* 位运算：

  运算符|描述|实例
  -|-|-
  &|按位与运算符|（a & b）输入结果12,二进制解释：0000 1100
  `|`|按位或运算符|（a `|` b）输入结果61,二进制解释：0011 1101
  ^|按位异或运算符|（a ^ b）输入结果49,二进制解释：0011 0001
  ~|按位取反运算符|（~a）输入结果-61,二进制解释：1100 0011
  <<|左移动运算符|a<<2输出结果240,二进制解释：1111 0000
  `>>`|右移动运算符|a>>2输出结果15,二进制解释：0000 1111

  ```python
    #!/usr/bin/python

    a = 60      # 60 = 0011 1100
    b = 13      # 13 = 0000 1101
    c = 0

    c = a & b;  # 12 = 0000 1100
    print "Line 1 - Value of c is ", c

    c = a | b;  # 61 = 0011 1101
    print "Line 2 - Value of c is ", c

    c = a ^ b;  # 49 = 0011 0001
    print "Line 3 - Value of c is ", c

    c = ~a;     # -61 = 1100 0011
    print "Line 4 - Value of c is ", c

    c = a << 2; # 240 = 1111 0000
    print "Line 5 - Value of c is ", c

    c = a >> 2; # 15 = 0000 1111
    print "Line 6 - Value of c is ", c
  ```

* 运算符优先级：

运算符|描述
  -|-
  **|指数（最高优先级）
  ~ + -|按位翻转,一元加号和减号（最后两个的方法名为+@和-@）
  `*` / % //|乘,除,取模和取整数
  `+` -|加法减法
  `>>` <<|右移,左移运算符
  &|位'AND'
  ^ `|`|位运算符
  <= < > >=|比较运算符
  <> == !=|等于运算符
  = %= /= //= -= += *= **=|赋值运算符
  is is not|身份运算符
  in not in|成员运算符
  not or and|逻辑运算符


* **三元运算**

  ```python
    result = 值1 if 条件 else 值2
  ```
  
  * 如果条件为真：`result` = 值1
  * 如果条件为假：`result` = 值2

## 13.表达式`if ... else`

* 场景1 用户登录验证

  ```python
    # 提示输入用户名和密码

    # 验证用户名和密码
    #   如果错误,则输出用户名或密码错误
    #   如果成功,则输出 欢迎,xxx!

    #!/usr/bin/env python
    # -*- coding: encoding -*-

    import getpass

    name = raw_input('请输入用户名：')
    pwd = getpass.getpass('请输入密码：')

    if name == "alex" and pwd == "cmd":
        print("欢迎，alex！")
    else:
        print("用户名和密码错误")
  ```

* 场景2 猜年龄游戏
  > 在程序里设定好的年龄，然后启动程序让用户猜测，用户出入后，根据他的输入提示用户输入的是否正确，如果错误，提示是猜大了还是猜小了

  ```python
  #!/usr/bin/env python
  # -*- coding: utf-8 -*-

  my_age = 28

  user_input = int(input("input your guess num:"))

  if user_input == my_age:
      print("Congratulations,you got it !")
  elif user_input < my_age:
      print("Oops,think bigger!")
  else:
      print("think smaller!")
  ```

  > 外层变量，可以被内层代码使用
  > 内层变量，不应被外层代码使用

## 14.表达式for loop

* 最简单的循环10次

  ```python
    #_*_coding:utf-8_*_
    __author__ = 'Alex Li'


    for i in range(10):
        print("loop:", i )
  ```

* 需求1：还是上面的程序，但是遇到小于5的循环次数就不走了，直接跳入下一次循环

  ```python
    for i in range(10):
        if i<5:
            continue  #不住下走了，直接进入下一次loop
        print("loop:",i )
  ```

* 需求2：还是上面的程序，但是遇到大于5的循环次数就不走了，直接退出

  ```python
    for i in range(10):
        if i>5:
            break  #不住下走了，直接跳出整个loop
        print("loop:", 1 )
  ```

## 15.while loop

> **有一种循环叫死循环，一经触发，就运行个天荒地老、海枯石烂**

  ```python
    count = 0
    while True:
        print("你是风儿我是沙,缠缠绵绵到天涯...",count)
        count += 1
  ```

> 其实除了时间，没有什么是永恒的，死loop还是少写为好
> 上面的代码循环100次就退出吧

  ```python
      count = 0
      while True:
          print("你是风儿我是沙,缠缠绵绵到天涯...",count)
          count +=1
          if count == 100:
              print("去你妈的风和沙,你们这些脱了裤子是人,穿上裤子是鬼的臭男人..")
              break
  ```

> 回到上面for 循环的例子，如何实现让用户不断的猜年龄，但只给最多3次机会，再猜不对就退出程序。

  ```python
    #!/usr/bin/env python
    # -*- coding: utf-8 -*-


    my_age = 28

    count = 0
    while count < 3:
        user_input = int(input("input your guess num:"))

        if user_input == my_age:
            print("Congratulations, you got it !")
            break
        elif user_input < my_age:
            print("Oops,think bigger!")
        else:
            print("think smaller!")
        count += 1 #每次loop 计数器+1
    else:
        print("猜这么多次都不对,你个笨蛋.")
  ```

## 16.练习

* 作业1：编写登陆接口
  > 1.输入用户名密码
  > 2.认证成功后显示欢迎信息
  > 3.输错三次后锁定

* 参考：[login.py](https://github.com/herhanhe/s14/blob/master/day01/login.py)

* 作业2：多级菜单
  > 1.三级菜单
  > 2.可依次选择进入各子菜单
  > 3.所需新知识点：列表、字典

* 参考：[menu.py](https://github.com/herhanhe/s14/blob/master/day01/menu.py)
  