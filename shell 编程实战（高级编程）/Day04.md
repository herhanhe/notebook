# `Shell`高级编程 Day04 —— Shell脚本的条件测试与比较
[toc]

## 1 Shell脚本的条件测试
### 1.1 条件测试方法综述
* 执行条件测试表达式后通常会返回“真”或“假”，就像执行命令后的返回值为0表示真，非0表示假。
* 条件测试常用的语法

条件测试语法|说明
-|-
语法1：`test <测试表达式>`|这是利用test命令进行条件测试表达式的方法。test命令和“<测试表达式>”之间至少有一个空格
语法2：`[ <测试表达式> ]`|这是通过`[]`（单中括号）进行条件测试表达式的方法，和test命令的用法相同。`[]`的边界和内容之间至少有一个空格。
语法3：`[[ <测试表达式> ]]`|这是通过`[[]]`（双中括号）进行条件测试表达式的方法，是比test和`[]`更新的语法格式。`[[]]`的边界和内容之间至少有一个空格。
语法4：`((<测试表达式>))`|这是通过`(())`（双小括号）进行条件测试表达式的方法，一般用于if语句里。`(())`（双小括号）两端不需要有空格。

* 注意事项：
  * 语法1中的test命令和语法2中的`[]`是等价的。语法3中的`[[]]`为扩展的test命令，语法4中的`(())`常用于计算
  * 在`[[]]`（双中括号）中可以使用通配符等进行模式匹配，这是其区别于其他几种语法格式的地方。
  * `&&`、`||`、`>`、`<`等操作符可以应用于`[]`中，在`[]`中一般用`-a`、`-o`、`-gt`（用于整数）、`-lt`（用于整数）代替上述操作符。
  * 对于整数的关系运算，也可以使用Shell的算术运算符`(())`。

### 1.2 test条件测试的简单语法及示例
* test条件测试的语法格式为：test <测试表达式>
* `test -f file && echo true || echo false`
  * 该语句表示如果`file`文件存在，则输出`true`，否则（`||`）输出`false`。这里的`&&`是并且的意思。`test`的`-f`参数用于测试文件是否为普通文件，`test`命令若执行成功（为真），则执行`&&`后面的命令，而`||`后面的命令是`test`命令执行失败之后（为假）所执行的命令
  * 逻辑操作符`&&`和`||`的两端既可以空格，也可以无空格

```bash
  # 在test命令中使用-f选项（文件存在且为普通文件则表达式成立）测试文件
  [root@backup ~]# test -f file && echo true || echo false
  false
  [root@backup ~]# touch file
  [root@backup ~]# test -f file && echo true || echo false
  true

  # 在test命令中使用-z选项（如果测试字符串的长度为0，则表达式成立）测试字符串
  [root@backup ~]# test -z "oldboy" && echo 1 || echo 0
  0
  [root@backup ~]# char="oldboy"
  [root@backup ~]# test -z "$char" && echo 1 || echo 0
  0
  [root@backup ~]# char=""
  [root@backup ~]# test -z "$char" && echo 1 || echo 0
  1
```

### 1.3 []（中括号）条件测试语法及示例
* `[]`条件测试的语法格式为：`[ <测试表达式> ]`
* `[ -f file ] && echo 1 || echo 0`
  * 该语句表示如果`file`文件存在，则输出`1`，否则（`||`）输出`0`。这里的`&&`是并且。`[]`的应用同`test`命令，若中括号里的命令执行成功（返回真），则执行`&&`后面的命令，否则执行`||`后面的命令。
  * `[]`测试表达式的逻辑也可以用如下的语法来判断逻辑的表达式写法（test命令的用法也适合于此）

```bash
  # 利用[]和-f选项（文件存在且为普通文件则表达式成立）测试文件，[]命令的选项和test命令的选项是通用的
  root@backup ~]# [ -f /tmp/herhan ] && echo 1 || echo 0
  0
  [root@backup ~]# touch /tmp/herhan
  [root@backup ~]# [ -f /tmp/herhan ] && echo 1 || echo 0
  1
  [root@backup ~]# [ -f /tmp/herhan ] && echo 1
  1
  [root@backup ~]# [ -f /tmp/herhan ] || echo 0
  [root@backup ~]# rm /tmp/herhan
  rm：是否删除普通空文件 "/tmp/herhan"？y
  [root@backup ~]# [ -f /tmp/herhan ] || echo 0
  0
```

### 1.4 [[]]条件测试语法及示例
* `[[]]`条件测试的语法格式为：`[[ <测试表达式> ]]`
* `[[ -f file ]] && echo 1 || echo 0`

```bash
  # [[]]的使用示例
  [root@backup ~]# [[ -f /tmp/herhan ]] && echo 1 || echo 0
  0
  [root@backup ~]# touch /tmp/herhan
  [root@backup ~]# [[ -f /tmp/herhan ]] && echo 1 || echo 0
  1
  [root@backup ~]# [[ -f /tmp/herhan ]] && echo 1
  1
  [root@backup ~]# rm -rf /tmp/herhan
  [root@backup ~]# [[ -f /tmp/herhan ]] || echo 0
  0
```

## 2 文件测试表达式
### 2.1 文件测试表达式的用法
* 常用的文件测试操作符
常用文件测试操作符|说明
-|-
`-d`文件，`d`的全拼为`directory`|文件存在且为目录则为真，即测试表达式成立
`-f`文件，`f`的全拼为`file`|文件存在且为普通文件则为真，即测试表达式成立
`-e`文件，`e`的全拼`exist`|文件存在则为真，即测试表达式成立。注意区别于`-f`,`-e`不辨别是目录还是文件
`-r`文件，`r`的全拼`read`|文件存在且可读则为真，即测试表达式成立
`-s`文件，`s`的全拼`size`|文件存在且文件大小不为0则为真，即测试表达式成立
`-w`文件，`w`的全拼`write`|文件存在且可写则为真，即测试表达式成立
`-x`文件，`x`的全拼`executable`|文件存在且可写则为真，即测试表达式成立
`-L`文件，`L`的全拼`link`|文件存在且为链接文件则为真，即测试表达式成立
`f1 -nt f2`，`nt`的全拼为`newer than`|文件f1比文件f2新则为真，即测试表达式成立。根据文件的修改时间来计算
`f1 -ot f2`，`ot`的全拼为`older than`|文件f1比文件f2旧则为真，即测试表达式成立。根据文件的修改时间来计算

### 2.2 文件测试表达式举例
* 1.普通文件测试表达式示例
  * (1)普通文件（测试文件类型）
    ```bash
      # 普通文件条件表达式测试实践
      [root@backup ~]# touch herhan
      [root@backup ~]# ls -l herhan
      -rw-r--r-- 1 root root 0 9月  12 18:48 herhan
      [root@backup ~]# [ -f herhan ] && echo 1 || echo 0
      1
    ```
  * (2)目录文件（测试文件类型）
    ```bash
      # 目录文件条件表达式测试实践
      [root@backup ~]# mkdir herh1
      [root@backup ~]# [ -f herh1 ] && echo 1 || echo 0
      0
      [root@backup ~]# [ -e herh1 ] && echo 1 || echo 0
      1
      [root@backup ~]# [ -d herh1 ] && echo 1 || echo 0
      1
    ```

* 2.测试文件属性示例
```bash
  # 文件属性条件表达式测试实践
  [root@backup ~]# ls -l herhan
  -rw-r--r-- 1 root root 0 9月  12 18:48 herhan
  [root@backup ~]# [ -r herhan ] && echo 1 || echo 0
  1
  [root@backup ~]# [ -w herhan ] && echo 1 || echo 0
  1
  [root@backup ~]# [ -x herhan ] && echo 1 || echo 0
  0
  [root@backup ~]# chmod 001 herhan
  [root@backup ~]# ls -l herhan
  ---------x 1 root root 0 9月  12 18:48 herhan
  [root@backup ~]# [ -x herhan ] && echo 1 || echo 0
  1
  [root@backup ~]# [ -w herhan ] && echo 1 || echo 0
  1
  [root@backup ~]# [ -r herhan ] && echo 1 || echo 0
  1
  [root@backup ~]# echo 'echo test' > herhan
  [root@backup ~]# cat herhan
  echo test
  [root@backup ~]# ./herhan 
  test
```

* 3 测试Shell变量示例
```bash
  # 首先定义file1和file2两个变量，并分别赋予这两个变量对应的系统文件路径及文件名的值，
  [root@backup ~]# file1=/etc/services;file2=/etc/rc.local
  [root@backup ~]# echo $file1 $file2
  /etc/services /etc/rc.local

  # 对单个文件变量进行测试
  [root@backup ~]# [ -f "$file1" ] && echo 1 || echo 0
  1
  [root@backup ~]# [ -d "$file1" ] && echo 1 || echo 0
  0
  [root@backup ~]# [ -s "$file1" ] && echo 1 || echo 0
  1
  [root@backup ~]# [ -e "$file1" ] && echo 1 || echo 0
  1

  # 对单个目录或文件进行测试
  [root@backup ~]# [ -e /etc ] && echo 1 || echo 0
  1
  [root@backup ~]# [ -w /etc/services ] && echo 1 || echo 0
  1
  [root@backup ~]# su - herhan
  [herhan@backup ~]$ [ -w /etc/services ] && echo 1 || echo 0
  0

  # 测试时变量的特殊写法及问题
  # 用[]测试变量时，如果被测试的变量不加双引号，那些测试结构结果可能会是不正确的
  [root@backup ~]# echo $herhan

  [root@backup ~]# [ -f $herhan ] && echo 1 || echo 0
  1
  [root@backup ~]# [ -f "$herhan" ] && echo 1 || echo 0
  0

  # 如果是文件实体路径，那么加引号与不加引号的结果是一样的
  [root@backup ~]# [ -f "/etc/services" ] && echo 1 || echo 0
  1
  [root@backup ~]# [ -f /etc/services ] && echo 1 || echo 0
  1

  # 在生产环境下，系统NFS启动脚本的条件测试
  # 实现系统bind启动脚本named
  # 写出简单高效的测试文件
  # 在做测试判断时，不一定非要按照“前面的操作成功了如何”的方法来进行。
  [root@backup ~]# [ -x herhan ] && echo 1
  1
  [root@backup ~]# [ -f /etc ] || echo 0
  0

  # 实现系统脚本/etc/init.d/nfs

```

### 2.3 特殊条件测试表达式案例
* 以下写法适用于所有的条件测试表达式，是工作中比较常用的替代if语句的方法。判断条件测试表达式的条件成立或不成立后，还需要继续执行多条命令语句的语法形式如下：
```bash
  # 当条件1成立时，同时执行命令1、命令2、命令3。不用if测试表达式的格式如下：
  [ 条件1 ] && {
    命令1
    命令2
    命令3
  }
  [[ 条件1 ]] && {
    命令1
    命令2
    命令3
  }
  test 条件1 && {
    命令1
    命令2
    命令3
  }

  # 上面的判断相当于下面if语句的效果
  if [ 条件1 ]
    then
      命令1
      命令2
      命令3
  fi

  # 当条件不成立时，执行大括号里的多条命令，这里要使用逻辑操作符“||”
  [root@backup ~]# cat 6_15.sh 
  [ -f /etc/ ] || {
          echo 1
          echo 2
          echo 3
  }
  [root@backup ~]# sh 6_15.sh 
  1
  2
  3
  [root@backup ~]# [ -f /etc/services ] && { echo "I am oldboy";echo "I am linuxer"; }
  I am oldboy
  I am linuxer
```

## 3 字符串测试表达式
### 3.1 字符串测试操作符
* 字符串测试操作符的作用包括：比较两个字符串是否相同、测试字符串的长度是否为零、字符串是否为NULL等。
* 在书写测试表达式时，可以使用表中的字符串测试操作符

常用字符串测试操作符|说明
-|-
`-n` "字符串"|若字符串的长度不为0，则为真，即测试表达式成立，n可以理解为`no zero`
`-z` "字符串"|若字符串的长度为0，则为真，即测试表达式成立，z可以理解为`zero`的缩写
"串1" = "串2"|若字符串1等于字符串2，则为真，即测试表达式成立，可使用"=="代替"="
"串1" != "串2"|若字符串1不等于字符串2，则为真，即测试表达式成立，但不能用"!=="代替"!="

* 注意
  * 对于字符串的测试，一定要将字符串加双引号之后再进行比较，特别是使用[]的场景
  * 比较符号的两端一定要有空格
  * "! ="和"="可用于比较两个字符串是否相同。

```bash
  # 字符串条件表达式测试实践
  [root@backup ~]# [ -n "abc" ] && echo 1 || echo 0
  1
  [root@backup ~]# test -n "abc"  && echo 1 || echo 0
  1
  [root@backup ~]# test -n ""  && echo 1 || echo 0
  0
  [root@backup ~]# var="herhan"
  [root@backup ~]# [ -n "$var" ] && echo 1 || echo 0
  1
  [root@backup ~]# [ -n $var ] && echo 1 || echo 0
  1
  [root@backup ~]# [ -z "$var" ] && echo 1 || echo 0
  0
  [root@backup ~]# [ "abc" = "abc" ] && echo 1 || echo 0
  1
  [root@backup ~]# [ "abc" = "abd" ] && echo 1 || echo 0
  0
  [root@backup ~]# [ "$var" = "herhan" ] && echo 1 || echo 0
  1
  [root@backup ~]# [ "$var" == "herhan" ] && echo 1 || echo 0
  1
  [root@backup ~]# [ "$var" != "herhan" ] && echo 1 || echo 0
  0

  # 进行字符串比较时，等号两端没有空格带来的问题
  [root@backup ~]# [ "herhan"="1" ] && echo 1 || echo 0
  1
  [root@backup ~]# [ "herhan" = "1" ] && echo 1 || echo 0
  0
  # 字符串比较时若等号两端没有空格，则会导致判断出现逻辑错误，即使语法没问题，但是结果依然可能不对

  # 字符串不加引号可能带来的问题
```

## 4 整数二元比较操作符
### 4.1 整数二元比较操作符介绍

在`[]`以及test中使用的比较符号|在`(())`和`[[]]`中使用的比较符号|说明
-|-|-
`-eq`|`==`或`=`|相等，全拼为`equal`
`-ne`|`!=`|不相等，全拼为`not equal`
`-gt`|`>`|大于，全拼为`greater than`
`-ge`|`>=`|大于等于，全拼为`greater equal`
`-lt`|`<`|小于，全拼为`less than`
`-le`|`<=`|小于等于，全拼为`less equal`

* 注意
  * "="和"! ="也可以在`[]`中做比较使用，但在`[]`中使用包含">"和"<"的符号时，需要用反斜线转义，有时不转义虽然语法不会报错，但是结果可能会不对。
  * 也可以在`[[]]`中使用包含"-gt"和"-lt"的符号，但是不建议这样使用。
  * 比较符号两端也要有空格。
```bash
  # 二元数字在`[]`中使用"<",">"非标准符号比较
  [root@backup ~]# [ 2 > 1 ] && echo 1 || echo 0
  1
  [root@backup ~]# [ 2 < 1 ] && echo 1 || echo 0
  1
  [root@backup ~]# [ 2 \< 1 ] && echo 1 || echo 0
  0
  [root@backup ~]# [ 2 = 1 ] && echo 1 || echo 0
  0
  [root@backup ~]# [ 2 = 2 ] && echo 1 || echo 0
  1
  [root@backup ~]# [ 2 != 2 ] && echo 1 || echo 0
  0

  # 二元数字在`[]`中使用-gt，-le类符号的比较
  [root@backup ~]# [ 2 -gt 1 ] && echo 1 || echo 0
  1
  [root@backup ~]# [ 2 -ge 1 ] && echo 1 || echo 0
  1
  [root@backup ~]# [ 2 -le 1 ] && echo 1 || echo 0
  0
  [root@backup ~]# [ 2 -lt 1 ] && echo 1 || echo 0
  0

  # 二元数字配合不同种类的操作符在`[[]]`中的比较
  [root@backup ~]# [[ 5 > 6 ]] && echo 1 || echo 0
  0
  [root@backup ~]# [[ 5 < 6 ]] && echo 1 || echo 0
  1
  [root@backup ~]# [[ 5 = 6 ]] && echo 1 || echo 0
  0
  [root@backup ~]# [[ 5 != 6 ]] && echo 1 || echo 0
  1
  [root@backup ~]# [[ 5 -gt 6 ]] && echo 1 || echo 0
  0
  [root@backup ~]# [[ 5 -lt 6 ]] && echo 1 || echo 0
  1
  [root@backup ~]# [[ 65 > 66 ]] && echo 1 || echo 0
  0
  [root@backup ~]# [[ 65 < 66 ]] && echo 1 || echo 0
  1
  [root@backup ~]# [[ 65 = 66 ]] && echo 1 || echo 0
  0

  # 二元数字在(())中的比较
  [root@backup ~]# ((3>2)) && echo 1 || echo 0
  1
  [root@backup ~]# ((3<2)) && echo 1 || echo 0
  0
  [root@backup ~]# 
  [root@backup ~]# ((3==2)) && echo 1 || echo 0
  0
  [root@backup ~]# ((3!==2)) && echo 1 || echo 0
  -bash: ((: 3!==2: 语法错误: 期待操作数 （错误符号是 "=2"）
  0
  [root@backup ~]# ((3!=2)) && echo 1 || echo 0
  1
```

* 有关`[]`、`[[]]`、`(())`用法小结：
  * 整数加双引号的比较是对的
  * `[[]]`中用类似-eq等的写法是对的，`[[]]`中用类似`>`、`<`的写法也可能不对，有可能会只比较第一位，逻辑结果不对。
  * `[]`中用类似`>`、`<`的写法在语法上虽然可能没错，但逻辑结果不对，可以使用`=`、`! =`正确比较。
  * `(())`中不能使用类似-eq等的写法，可以使用类似`>`、`<`的写法

### 4.2 整数变量测试实践示例
```bash
  # 通过`[]`实现整数条件测试
  [root@backup ~]# a1=98;a2=99
  [root@backup ~]# [ $a1 -eq $a2 ] && echo 1 || echo 0
  0
  [root@backup ~]# [ $a1 -gt $a2 ] && echo 1 || echo 0
  0
  [root@backup ~]# [ $a1 -lt $a2 ] && echo 1 || echo 0
  1

  # 利用`[[]]`和`(()`实现直接通过常规数学运算符进行比较
  [root@backup ~]# [[ $a1 > $a2 ]] && echo 1 || echo 0
  0
  [root@backup ~]# [[ $a1 < $a2 ]] && echo 1 || echo 0
  1
  [root@backup ~]# (($a1>=$a2)) && echo 1 || echo 0
  0
  [root@backup ~]# (($a1<=$a2)) && echo 1 || echo 0
  1
```

## 5 逻辑操作符
### 5.1 逻辑操作符介绍
在`[]`和test中使用的操作符|在`[[]]`和`(())`中使用的操作符|说明
-|-|-
`-a`|`&&`|and,与,两端都为真，则结果为真
`-o`|`||`|or,或,两端有一个为真，则结果为真
`!`|`!`|not,非,两端相反，则结果为真

* 注意
  * 逻辑操作符前后的表达式是否成立，一般用真假来表示。
  * "!"的中文意思是反，即与一个逻辑值相反的逻辑值。
  * `-a`的中文意思是"与"（and或&&），前后两个逻辑值都为真，综合返回值才为真，反之为假。
  * `-o`的中文意思是"或"（or或||），前后两个逻辑值只要有一个为真，返回值就为真。
  * 连接两含`[]`、test或`[[]]`的表达式可用&&或||。

### 5.2 逻辑操作符实践示例

```bash
  # `[]`里的逻辑操作符配合文件测试表达式使用的示例
  [root@backup ~]# f1=/etc/rc.local;f2=/etc/services
  [root@backup ~]# echo -ne "$f1 $f2\n"
  /etc/rc.local /etc/services
  [root@backup ~]# [ -f "$f1" -a -f "$f2" ] && echo 1 || echo 2
  1
  [root@backup ~]# [ -f "$f1" -a -f "$f2" ] && echo 1 || echo 0
  1
  [root@backup ~]# [ -f "$f1" -a -f "$f222" ] && echo 1 || echo 0
  0
  [root@backup ~]# [ -f "$f111" -a -f "$f222" ] && echo 1 || echo 0
  0
  [root@backup ~]# [ -f "$f1" && -f "$f2" ] && echo 1 || echo 0
  -bash: [: 缺少 `]'
  0
  [root@backup ~]# [ -f "$f1" ] && [ -f "$f2" ] && echo 1 || echo 0
  1

  # `[[]]`里的逻辑操作符配合字符串的条件表达式
  [root@backup ~]# a="oldboy";b="oldgirl"
  [root@backup ~]# echo -ne "$a $b\n"
  oldboy oldgirl
  [root@backup ~]# [[ ! -n "$a" && "$a" = "$b" ]] && echo 1 || echo 0
  0
  [root@backup ~]# [[ -z "$a" && "$a" != "$b" ]] && echo 1 || echo 0
  0
  [root@backup ~]# [[ -z "$a" || "$a" != "$b" ]] && echo 1 || echo 0
  1
  [root@backup ~]# [[ -z "$a" -o "$a" != "$b" ]] && echo 1 || echo 0
  -bash: 条件表达式中有语法错误
  -bash: `-o' 附近有语法错误

  # `(())`里的逻辑操作符配合整数的条件表达式测试示例
  [root@backup ~]# m=21;n=38
  [root@backup ~]# ((m>20&&n>30)) && echo 1 || echo 0
  1
  [root@backup ~]# ((m<20||n<30)) && echo 1 || echo 0
  0
  [root@backup ~]# ((m<20||n>30)) && echo 1 || echo 0
  1
  [root@backup ~]# ((m<20 -a n<30)) && echo 1 || echo 0
  -bash: ((: m<20 -a n<30: 表达式中有语法错误 （错误符号是 "n<30"）
  0

  # 使用多个`[]`号，并通过与或非进行混合测试
  [root@backup ~]# m=21;n=38
  [root@backup ~]# [ $m -gt 20 -a $n -lt 30 ] && echo 1 || echo 0
  0
  [root@backup ~]# [ $m -gt 20 ]||[ $n -lt 30 ] && echo 1 || echo 0
  1

  # 系统启动脚本中有关`[[]]`的用法和与或非判断的使用案例
  [root@backup ~]# for n in `ls /etc/init.d/*`;do egrep -wn "\[\[ " $n&&echo $n;done
  57:     if [[ $route == *" via "* ]] ; then
  82:             if ! [[ "$SYSLOGADDR" =~ $IPv4_regex ]] && ! [[ "$SYSLOGADDR" =~ $IPv6_regex ]]; then
  87:                     if [[ $? -eq 2 ]]; then
  91:                     if [[ $? -ne 0 ]]; then
  /etc/init.d/netconsole
  168:    if [[ "${root_fstype}" == nfs* || "${usr_fstype}" == nfs* ]] || systemctl show --property=RequiredBy -- -.mount usr.mount | grep -q 'remote-fs.target' ; then
  /etc/init.d/network
```

### 5.3 逻辑操作符企业案例
```bash
  # 输入或通过命令行传入一个字符或数字，如果传入的数字等于1，就打印1；如果等于2，就打印2；如果不等于1也不等于2，就提示输入不对，然后退出程序。
  # 使用read读入内容方案。
  [root@backup ~]# cat 6_34_1.sh
  #!/bin/sh
  echo -n "pls input a char:"
  read var
  [ "$var" == "1" ] && {
          echo 1
          exit 0
  }
  [ "$var" == "2" ] && {
          echo 2
          exit 0
  }
  [ "$var" != "2" -a "$var" != "1" ] && {
          echo error
          exit 0
  }
  [root@backup ~]# sh 6_34_1.sh
  pls input a char:1
  1
  [root@backup ~]# sh 6_34_1.sh
  pls input a char:2
  2
  [root@backup ~]# sh 6_34_1.sh
  pls input a char:4
  error

  # 使用脚本命令行传参读入内容的解决方案
  [root@backup ~]# cat 6_34_2.sh
  #!/bin/sh
  var=$1
  [ "$var" == "1" ] && {
          echo 1
          exit 0
  }
  [ "$var" == "2" ] && {
          echo 2
          exit 0
  }
  [ "$var" != "2" -a "$var" != "1" ] && {
          echo error
          exit 0
  }
  [root@backup ~]# sh 6_34_2.sh 1
  1
  [root@backup ~]# sh 6_34_2.sh 2
  2
  [root@backup ~]# sh 6_34_2.sh 4
  error

  # 开发Shell脚本，分别实现以脚本传参和read读入的方式比较两个整数的大小。用条件表达式（禁止用if）进行判断并以屏幕输出的方式提醒用户比较的结果。注意：一共是开发两个脚本。在用脚本传参和read读入的方式实现时，需要对变量是否为数字及传参个数是否正确给予提示
  # 采用read方法
  [root@backup ~]# cat 6_35_1.sh
  #!/bin/sh
  read -p "PLs input two num:" a b
  # no1
  [ -z "$a" ]||[ -z "$b" ] && {
          echo "PLs input two num again."
          exit 1
  }
  # no2
  expr $a + 10 &>/dev/null
  RETVAL1=$?
  expr $b + 10 &>/dev/null
  RETVAL2=$?
  test $RETVAL1 -eq 0 -a $RETVAL2 -eq 0 || {
          echo "Pls input two "num" again."
          exit 2
  }
  # no3
  [ $a -lt $b ] && {
          echo "$a < $b"
          exit 0
  }
  # no4
  [ $a -eq $b ] && {
          echo "$a = $b"
          exit 0
  }
  # no5
  [ $a -gt $b ] && {
          echo "$a > $b"
  }
  [root@backup ~]# sh 6_35_1.sh
  PLs input two num:6 2
  6 > 2
  [root@backup ~]# sh 6_35_1.sh
  PLs input two num:6 6
  6 = 6
  [root@backup ~]# sh 6_35_1.sh
  PLs input two num:2 6
  2 < 6
  [root@backup ~]# sh 6_35_1.sh
  PLs input two num:
  PLs input two num again.
  [root@backup ~]# sh 6_35_1.sh
  PLs input two num:dd ff
  Pls input two num again.

  # 通过命令行传参的方法实现
  [root@backup ~]# sh 6_35_2.sh
  USAGE:6_35_2.sh NUM1 NUM2
  [root@backup ~]# sh 6_35_2.sh 6 2
  6 > 2
  [root@backup ~]# sh 6_35_2.sh 6 6
  6 = 6
  [root@backup ~]# sh 6_35_2.sh 2 6
  2 < 6
  [root@backup ~]# cat 6_35_2.sh
  #!/bin/sh
  a=$1
  b=$2
  # no1
  [ $# -ne 2 ] && {
          echo "USAGE:$0 NUM1 NUM2"
          exit 1
  }
  # no2
  expr $a + 10 &>/dev/null
  RETVAL1=$?
  expr $b + 10 &>/dev/null
  RETVAL2=$?
  test $RETVAL1 -eq 0 -a $RETVAL2 -eq 0 || {
          echo "Pls input two "num" again."
          exit 2
  }
  # no3
  [ $a -lt $b ] && {
          echo "$a < $b"
          exit 0
  }
  # no4
  [ $a -eq $b ] && {
          echo "$a = $b"
          exit 0
  }
  # no5
  [ $a -gt $b ] && {
          echo "$a > $b"
  }

  # 打印选择菜单，按照选择项一键安装不同的Web服务
  # 示例菜单：
  # 1.[install lamp]
  # 2.[install lnmp]
  # 3.exit
  # pls input the num you want:
  # 要求：
  # 1）当用户输入1时，输出"start installing lamp"提示，然后执行`/server/scripts/lamp.sh`输出"lamp is installed",并退出脚本，此为工作中所用的lamp一键安装脚本。
  # 2）当用户输入2时，输出"start installing lnmp"提示，然后执行`/server/scripts/lnmp.sh`输出"lnmp is installed",并退出脚本，此为工作中所用的lnmp一键安装脚本。
  # 3）当输入3时，退出当前菜单及脚本
  # 4）当输入任何其他字符时，给出提示"Input error"后退出脚本。
  # 5）对执行的脚本进行相关的条件判断，例如：脚本文件是否存在，是否可执行等的判断，尽量使用前面讲解过的知识点
  [root@backup ~]# cat 6_36_1.sh 
  cat <<END
          1.panxiaoting
          2.gongli
          3.fanbinbing
  END
  read -p "Which do you like？Pls input the num:" a
  [ "$a" = "1" ] && {
          echo "I guess,you like panxiaoting"
          exit 0
  }
  [ "$a" = "2" ] && {
          echo "I guess,you like gongli"
          exit 0
  }
  [ "$a" = "3" ] && {
          echo "I guess,you like fangbingbing"
          exit 0
  }
  [[ ! "$a" =~ [1-3] ]] && {
          echo "I guess,you are not man."
  }
  [root@backup ~]# sh 6_36_1.sh
        1.panxiaoting
        2.gongli
        3.fanbinbing
  Which do you like？Pls input the num:1
  I guess,you like panxiaoting
  [root@backup ~]# sh 6_36_1.sh
          1.panxiaoting
          2.gongli
          3.fanbinbing
  Which do you like？Pls input the num:2
  I guess,you like gongli
  [root@backup ~]# sh 6_36_1.sh
          1.panxiaoting
          2.gongli
          3.fanbinbing
  Which do you like？Pls input the num:3
  I guess,you like fangbingbing
  [root@backup ~]# sh 6_36_1.sh
          1.panxiaoting
          2.gongli
          3.fanbinbing
  Which do you like？Pls input the num:4
  I guess,you are not man.

  # 
  [root@backup ~]# sh menu.sh 
          1.[install lamp]
          2.[install lnmp]
          3.[exit]
          pls input the num you want:
  [root@backup ~]# cat menu.sh
  menu(){
  cat << END
          1.[install lamp]
          2.[install lnmp]
          3.[exit]
          pls input the num you want:
  END
  }
  menu

  # 正式
  [root@backup ~]# mkdir -p /server/scripts
  [root@backup ~]# cd /server/scripts/
  [root@backup scripts]# echo "echo lamp is installed" >lamp.sh
  [root@backup scripts]# echo "echo lnmp is installed" >lnmp.sh
  [root@backup scripts]# chmod +x lnmp.sh lamp.sh
  [root@backup scripts]# cat 6_36_2.sh 
  #!/bin/sh
  path=/server/scripts
  [ ! -d "$path" ] && mkdir $path -p
  #menu
  cat <<END
          1.[install lamp]
          2.[install lnmp]
          3.[exit]
          pls input the num you want:
  END
  read num
  expr $num + 1 &>/dev/null
  [ $? -ne 0 ] && {
          echo "the num you input must be {1|2|3}"
          exit 1
  }
  [ $num -eq 1 ] && {
          echo "start installing lamp."
          sleep 2;
          [ -x "$path/lamp.sh" ] || {
                  echo "$path/lamp.sh does not exist or can not be exec."
                  exit 1
          }
          $path/lamp.sh
          exit $?
  }
  [ $num -eq 2 ] && {
          echo "start installing lnmp."
          sleep 2;
          [ -x "$path/lnmp.sh" ] || {
                  echo "$path/lnmp.sh does not exist or can not be exec."
                  exit 1
          }
          $path/lnmp.sh
          exit $?
  }
  [ $num -eq 3 ] && {
          echo bye.
          exit 3
  }
  [[ ! $num =~ [1-3] ]] && {
          echo "the num you input must be {1|2|3}"
          echo "Input ERROR"
          exit 4
  }
```
## 6 测试表达式test、`[]`、`[[]]`、`(())`的区别总结
测试表达式符号|`[]`|`test`|`[[]]`|`(())`
-|-|-|-|-
边界为是否需要空格|需要|需要|需要|不需要
逻辑操作符|`!,-a,-o`|`!,-a,-o`|`!`,`&&`|`!`,`&&`
整数比较操作符|`-eq -gt -lt -ge -le`|`-eq -gt -lt -ge -le`|`-eq -gt -lt -ge -le 或 = > < >= <=`|`= > < >= <=`
字符串比较操作符|`= == !=`|`= == !=`|`= == !=`|`= == !=`
是否支持通配符匹配|不支持|不支持|支持|不支持