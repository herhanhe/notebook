---
title: c常用的函数
tags: 学习
notebook: c
---

# c常用的函数


### （1）rand 函数：生成随机数
>**rand** <br/>
>头文件： `#include <stdio.h>`<br/>
>格式：&emsp;`int rand(void);`<br/>
>功能：&emsp;计算`0~RAND_MAX`的伪随机整数序列。此外，其他库函数在运行时会无视本函数的调用<br/>
>返回值：返回生成的伪随机数整数
	
```c
	#define RAND_MAX  32767    /*定义的示例：值根据编程环境而有所差别*/
```
### （2）srand 函数：设置用于生成随机数的种子
>**srand**<br/>
>头文件：` #include <stdlib.h>`<br/>
>格式：&emsp;`void srand(unsigned seed);`<br/>
>功能：&emsp;给后续调用的rand函数设置一个种子（`seed`），用于生成新的伪随机数序列。如果用同一个种子的值调用本函数，就会生成相同的伪随机数序列。如果在调用本函数之前调用了`rand`函数，就相当于程序在一开始调用了本函数，把`seed`设定成了`1`，最后会生成一个种子值为`1`的序列。此外，其他库函数在运行时会无视本函数的调用<br/>
>返回值：无
	
### （3）putchar 函数：输出字符
>**putchar**<br/>
>头文件：`#include <stdio.h>`<br/>
>格式：&emsp;`int putchar(int c);`<br/>
>功能：&emsp;作为第2实参，等价于指定了`stdout`的`putc`函数<br/>
>返回值：返回写入的字符。一旦发生写入错误，就设置该流的错误指示符并返回`EOF`
	
### （4）clock函数：获取程序启动后经过的时间**
>**clock**<br/>
>头文件：`#include <time.h>`<br/>
>格式：&emsp;`clock_t clock(void);`<br/>
>功能：&emsp;求处理器调用某个进程所花费的时间<br/>
>返回值：从定义与程序启动相关的编程环境的时间点起，用处理系统的最佳逼近返回程序占用处理器的时间。为了以秒为计量单位，必须用本函数的返回值除`CLOCKS_PER_SEC`宏的值。如果无法获取处理器调用该进程所花费的时间，或无法显示数值，就返回值`(clock_t)-1`
```c	
	typedef unsigned clock_t;       /*定义实例：类型根据编程环境不同而有所差别*/
	#define CLOCKS_PER_SEC  1000        /*定义示例：值根据编程环境而有所差别*/
```
### （5）strlen 函数：查询字符串的长度**
>**strlen**<br/>
>头文件：`#include <string.h>`<br/>
>格式：&emsp;`size_t strlen(const char *s);`<br/>
>功能：&emsp;计算`s`所指定的字符串的长度<br/>
>返回值：返回表示末尾的空字符前面的字符的个数
	
### （6）printf 函数：格式输出
>**printf**<br/>
>头文件：`#include <stdio.h>`<br/>
>格式：&emsp;`int printf(const char *format,...);`<br/>
>功能：&emsp;`printf`函数会把`format`后面的实际参数转换成字符序列格式，输出到标准输出流。这个转换时根据`format`指定的格式字符串内的指令进行的，格式字符串内可以不包含任何指令，也可以包含多个指令。未定义实际的参数比格式字符串少时的行为。实际参数多于格式字符串时，只需要对多余的实际参数求值然后便可忽略。<br/>
>&emsp;&emsp;指令可分为以下两种。<br/>
>&emsp;&emsp;&emsp;&emsp;`*%`以外的字符，不进行转化，直接复制到输出流。<br/>
>&emsp;&emsp;&emsp;&emsp;`*`转化说明，对后面给出的0个以上的实际参数进行转换。<br/>
>&emsp;&emsp;&emsp;&emsp;`%`后面将依次出现下列的(a)~(e)。<br/>
>返回值：返回已输出的字符数。发生输出错误时则返回负值。

### （7）scanf 函数：格式输入
>**scanf**<br/>
>头文件：`#include <stdio.h>`<br/>
>格式：&emsp;`int scanf(const char *format,...);`<br/>
>功能：&emsp;对从标准输入流输入的信息进行转化，并将结果存入`format`后面的实际参数指向的对象中。`format`指向字;符串为格式字符串，指定了允许输入的字符串和赋值时的转换方法。格式字符串内可以不包含任何指令，也可以包含多个指。未定义实际参数少于格式字符串的行为。实际参数多于格式字符串时，只需对多余的实际参数求值然后便可忽略。<br/>
>&emsp;&emsp;指令可分为以下3种<br/>
>&emsp;&emsp;&emsp;&emsp;`*`一个以上（包含1个）的空白字符。<br/>
>&emsp;&emsp;&emsp;&emsp;`*`（`%`和空白字符以外的）字符<br/>
>&emsp;&emsp;&emsp;&emsp;`*`转换说明<br/>
>&emsp;&emsp;&emsp;&emsp;`%`后面将依次出现下列的(a)~(d)。<br/>
>返回值：如果在没有进行任何转换的情况下发生了输入错误，函数会返回宏`EOF`的值，否则返回成功赋值的输入项数。如果在输入中发生了匹配错误，则输入项数会少于转换说明符对应的实际参数的数量，或是变成0。
				
### （8）CHAR_BIT 对象宏:表示该环境中1个字节的位数
>头文件：`#include <limits.h>`<br/>
>格式：&emsp;`#define CHAR_BIT   8  /*定义示例*/`

### （9）atoi 函数 / atol 函数 / atof 函数：把字符串转换为数值
>**atoi**<br/>
>头文件：`#include <stdlib.h>`<br/>
>格式：&emsp;`int atoi(const char *nptr);`<br/>
>功能：&emsp;把`nptr`所指的字符串转换成`int`型的形式。<br/>
>返回值：返回转换后的值。若无法用`int`型表示结果数值，则作未定义处理（取决于编程环境）。
				
>**atol**<br/>
>头文件：`#include <stdlib.h>`<br/>
>格式：&emsp;`long atol(const char *nptr);`<br/>
>功能：&emsp;把`nptr`所指的字符串转换成`long`型的形式。<br/>
>返回值：返回转换后的值。若无法用`long`型表示结果数值，则作未定义处理（取决于编程环境）。

>**atof**<br/>
>头文件：`#include <stdlib.h>`<br/>
>格式：&emsp;`double atof(const char *nptr);`<br/>
>功能：&emsp;把`nptr`所指的字符串转换成`double`型的形式。<br/>
>返回值：返回转换后的值。若无法用double型表示结果数值，则作未定义处理（取决于编程环境）。

### （11）iscntrl 函数 / isprint 函数 / isgraph 函数 / isdigit 函数 / isupper 函数 / islower 函数 / isalpha 函数 / isalnum 函数 / 
&emsp;**ispunct 函数 / isxdigit 函数 / isspace 函数：字符类别的判断**
>**iscntrl**<br/>
>头文件：`#include <ctype.h>`<br/>
>格式：&emsp;`int iscntrl(int c)`<br/>
>功能：&emsp;判断`c`是否为控制字符。<br/>
>返回值：若判断成立，则返回除0以外的值（真），若判断不成立，则返回0。<br/>

>**isprint**<br/>
>头文件：`#include <ctype.h>`<br/>
>格式：&emsp;`int isprint(int c);`<br/>
>功能：&emsp;判断`c`是否为含有空白字符(`''`)的显示字符。<br/>
>返回值：如果判断成立，就返回除`0`以外的值(真),如果不成立则返回`0`。<br/>

>**isgraph**<br/>
>头文件：`#include <ctype.h>`<br/>
>格式：&emsp;`int isgraph(int c);`<br/>
>功能：&emsp;判断字符`c`是否为不包括空白字符的显示字符。<br/>
>返回值：若判断成立，则返回除`0`以外的值（真），若判断不成立，则返回`0`。<br/>

>**isdigit**<br/>
>头文件：`#include <ctype.h>`<br/>
>格式：&emsp;`int isdigit(int c);`<br/>
>功能：&emsp;判断`c`是否为十进制数字字符。<br/>
>返回值：若判断成立，则返回除`0`以外的值（真），若判断不成立，则返回`0`。<br/>
				
>**isupper**<br/>
>头文件：`#include <ctype.h>`<br/>
>格式：&emsp;`int isupper(int c);`<br/>
>功能：&emsp;判断`c`是否为大写英文字符。<br/>
>返回值：若判断成立，则返回除`0`以外的值（真），若判断不成立，则返回`0`。<br/>
				
>**islower**<br/>
>头文件：`#include <ctype.h>`<br/>
>格式：&emsp;`int islower(int c);`<br/>
>功能：&emsp;判断字符c是否为小写英文字符。<br/>
>返回值：若判断成立，则返回除`0`以外的值（真），若判断不成立，则返回`0`。<br/>

>**isalpha**<br/>
>头文件：`#include <ctype.h>`<br/>
>格式：&emsp;`int isalpha(int c);`<br/>
>功能：&emsp;判断`c`是否为英文字符。<br/>
>返回值：若判断成立，则返回除`0`以外的值（真），若判断不成立，则返回`0`。<br/>
				
>**isalnum**<br/>
>头文件：`#include <ctype.h>`<br/>
>格式：&emsp;`int isalnum(int c);`<br/>
>功能：&emsp;判断`c`是否为英文字符或十进制数字。<br/>
>返回值：若判断成立，则返回除`0`以外的值（真），若判断不成立，则返回`0`。<br/>
				
>**ispunct**<br/>
>头文件：`include <ctype.h>`<br/>
>格式：&emsp;`int ispunct(int c);`<br/>
>功能：&emsp;判断`c`是否为非空白字符其非数字字符且非英文字符的显示字符。<br/>
>返回值：若判断成立，则返回除`0`以外的值（真），若判断不成立，则返回`0`。<br/>
				
>**isxdigit**<br/>
>头文件：`#include <ctype.h>`<br/>
>格式：&emsp;`int isxdigit(int c);`<br/>
>功能：&emsp;判断`c`是否为十六进制数字字符。<br/>
>返回值：若判断成立，则返回除`0`以外的值（真），若判断不成立，则返回`0`。<br/>
				
>**isspace**<br/>
>头文件：`#include <ctype.h>`<br/>
>格式：&emsp;`int isspace(int c);`<br/>
>功能：&emsp;判断`c`是否为空白类字符（空白字符`''`、换页符`'\f'`,换行符`'\n'`,回车符`'\r'`,水平制表符`'\t'`,垂直制表符`'\v'`）。<br/>
>返回值：若判断成立，则返回除`0`以外的值（真），若判断不成立，则返回`0`。<br/>
	
### （12）strcmp 函数 / strncmp 函数：字符串的比较
>**strcmp**<br/>
>头文件：`#include <string.h>`<br/>
>格式：&emsp;`int strcmp(const char *s1,const char *s2);`<br/>
>功能：&emsp;比较`s1`所指的字符串和`s2`所指的字符串的大小关系（从第一个字符开始逐一进行比较，当出现不同字时，以这对不同字符的大小关系为准）。<br/>
>返回值：若`s1`和`s2`相等则返回`0`；若`s1`大于`s2`则返回正整数值；若`s1`小于`s2`则返回负整数值。

```c
	/*strcmp函数的实现示例*/
	int strcmp(const char *s1, const char *s2){
		while (*s1 == *s2){
		    if (*s1 == '\0')                /*相等*/
		        return 0;
		    s1++;
		    s2++;
		    }
		return (unsigned char)*s1 - (unsigned char)*s2;
	}
```				

>**strncmp**<br/>
>头文件：`#include <string.h>`<br/>
>格式：&emsp;`int strncmp(const char *s1,const char *s2,size_t n);`<br/>
>功能：&emsp;比较`s1`所指的字符串和`s2`所指的字符串的前`n`个字符的大小关系（从第一个字符开始逐一进行比较，当出现不同字符时，以这对不同字符的大小关系为准）。<br/>
>返回值：若`s1`和`s2`相等则返回`0`；若`s1`大于`s2`则返回正整数值；若`s1`小于`s2`则返回负整数值。<br/>
```c
	/*strncmp函数的实现示例*/
	#include <stdio.h>
	int strncmp(const char *s1, const char *s2, size_t n){
	    while (n && *s1 && *s2){
		    if (*s1 != *s2)                /*不相等*/
		        return ((unsigned char)*s1 - (unsigned char)*s2);
		    s1++;
		    s2++;
		    n--;
		}
		if (!n) return 0;
		if (*s1) return 1;
		return -1;
	}
```
```c
	typedef unsigned size_t;			/*定义示例*/
```

### （13）存储空间的动态分配与释放
>**calloc**<br/>
>头文件：`#include <stdlib.h>`<br/>
>格式：&emsp;`void *calloc(size_t nmemb,size_t size);`<br/>
>功能：&emsp;为`nmemb`个大小为`size`字节的对象分配存储空间，该空间内的所有位都会初始化为`0`。<br/>
>返回值：若分配成功，则返回一个指向已分配的空间开头的指针;若分配失败，则返回空指针。<br/>
				
>**malloc**<br/>
>头文件：`#include <stdlib.h>`<br/>
>格式：&emsp;`void *malloc(size_t size);`<br/>
>功能：&emsp;为大小为`size`字节的对象分配存储空间，此存储空间中的初始值不确定。<br/>
>返回值：若分配成功，则返回一个指向已分配的空间开头的指针;若分配失败，则返回空指针。<br/>
				
>**free**<br/>
>头文件：`#include <stdlib.h>`<br/>
>格式：&emsp;`void free(void *ptr);`<br/>
>功能：&emsp;释放`ptr`指向的空间，让这部分空间能继续用于之后的动态分配。当`ptr`为空指针时，不执行任何操作。除此之外，当实际参数与之前通过`calloc`函数、`malloc`函数、`realloc`函数返回的指针不一致时，或者`ptr`指向的空间已经通过调用`free`或`realloc`被释放时，则作未定义处理。<br/>
>返回值：无<br/>

### （14）time函数:以日历时间的形式来获取当前时间
>**time**<br/>
>头文件：`#include <time.h>`<br/>
>格式：&emsp;`time_t time(time_t *timer);`<br/>
>功能：&emsp;决定当前的日历时间。未定义该值的表现形式。<br/>
>返回值：用所在编程环境中的最佳逼近返回求出的日历时间。若日历时间无效则返回值`(time_t)-1`	，当`timer`不为空指针时，将返回值赋给`timer`指向的对象。
```c
	typedef long  time_t;     /*日历类型：根据编程环境不同而有所不同*/
```
```c
	/*tm结构体：分解时间 ·· 根据编程环境不同而有所不同*/
	struct tm {
            int  tm_sec;   /*秒（0~61）*/
            int  tm_min;   /*分（0~59）*/
            int  tm_hour;  /*时（0~23）*/
            int  tm_mday;  /*日（0~31）*/
            int  tm_mon;   /*从1月开始的月份（0~11）*/
            int  tm_year;  /*从1900开始的年份*/
            int  tm_wday;  /*星期：星期日~星期六（0~6）*/
            int  tm_yday;  /*从1月1日开始的天数（0~365）*/
            int  tm_isdst; /*夏令时标志*/
	}
```
### 15）localtime 函数：把日历时间转换成表示本地时间的分解时间
>**localtime**<br/>
>头文件：`#include <time.h>`<br/>
>格式：&emsp;`struct tm *localtime(const time_t *timer);`<br/>
>功能：&emsp;把`timer`指向的日历时间转换成用本地时表示的分解时间。<br/>
>返回值：返回指向转换后的对象指针<br/>

### （16）gmtime 函数：把日历时间转换成UTC（协调世界时）分解时间
>**gmtime**<br/>
>头文件：`#include <time.h>`<br/>
>格式：&emsp;`struct tm *gmtime(const time_t *timer);`<br/>
>功能：&emsp;把`timer`指向的日历时间转换成用协调世界时表示的分解时间。<br/>
>返回值：返回指向转换后的对象的指针<br/>

### （17）asctime 函数：把分解时间转换成字符串
>**asctime**
>头文件：`#include <time.h>`<br/>
>格式：&emsp;`char *asctime(const struct tm *timeptr);`<br/>
>功能：&emsp;把`timeptr`指向的结构体的分解时间转换成下面这种形式的字符串。`Sun Sep 16 01:03:52 1973\n\0`<br/>
> 返回值：返回指向转换后的对象的指针<br/>

### （18）ctime 函数：把日历时间转换成字符串
>**ctime**<br/>
>头文件：`#include <time.h>`<br/>
>格式：&emsp;`char *ctime(const time_t *timer);`<br/>
>功能：&emsp;把`timer`指向的日历时间转换成与`asctime`函数具有相同字符串形式的本地时间，相当于`asctime(loacltime(timer))`<br/>
>返回值：返回以分解时间为实际参数的`asctime`函数返回的指针<br/>

### （19）difftime 函数：求时间差
>**difftime**<br/>
>头文件：`#include <time.h>`<br/>
>格式：&emsp;`double difftime(time_t time1,time_t time0);`<br/>
>功能：&emsp;计算两个日历时间的差`time1 - time0`<br/>
>返回值：以秒为单位表示求得的时间差，将其作为`double`型返回<br/>

### （20）mktime 函数：把表示本地时间的分解时间转换成日历时间
>**mktime**<br/>
>头文件：`#include <time.h>`<br/>
>格式：&emsp;`time_t mktime(struct tm *timeptr);`<br/>
>功能：&emsp;把表示`timeptr`指向的结构体中的本地时间的分解时间转换成与`time`函数的返回值具有相同表现形式的日历时间值。忽略结构体`tm_wday`以及`tm_yday`元素的值。其他元素的值可以不在`tm`结构体的定义中能中注释所示的值的范围内。当函数正常运行结束后，适当地设定结构体`tm_wday`以及`tm_yday`元素的值，其他元素则设定成用于表示指定的日历时间。这些值会被强制归纳在注释所示的范围内。这里在决定`tm_mon`以及`tm_year`的值之前并不设定`tm_mday`的最终值<br/>
>返回值：把指定的分解时间转换成`time_t`型的值的表现形式并返回。当无法用日历时间表示时，函数会返回值`(time_t)-1`<br/>

### （21）sprintf 函数：对字符串进行格式化输出
>**sprintf**<br/>
>头文件：`#include <stdio.h>`<br/>
>格式：&emsp;`int sprintf(char *s,const char *format,...);`<br/>
>功能：&emsp;除了数据的写入方向是s指向的数组而不是标准输出流之外，其他与`printf`函数相同。虽然在已入的输入字符的末尾会添加空字符，但统计返回的字符数时不会将该空字符计算在内。在空间重叠的对象间进行复制操作时，作未定义处理<br/>
>返回值：返回已写入数组的不包含空字符的字符数<br/>

### （22）strcpy 函数：字符串的复制
>**strcpy**<br/>
>头文件：`#include <string.h>`<br/>
>格式：&emsp;`char *strcpy(char *s1, const char *s2);`<br/>
>功能：&emsp;把s2指向的字符串复制到s1指向的数组。当s2与s1重叠时，作未定义处理<br/>
>返回值：返回s1的值
```c
    /*strcpy函数的运行示例*/
    char *strcpy(char *s1,const char *s2){
        char *p = s1;
        while (*s1++ = *s2++)
            ;
        return p;
    }
```

### （23）strcat 函数：字符串的连接
>**strcat**<br/>
>头文件：`#include <string.h>`<br/>
>格式：&emsp;`char *strcat(char *s1,const char *s2);`<br/>
>功能：&emsp;把`s2`指向的字符串复制到s1指向的数组的末尾。当s2与s1重叠时，作未定义处理<br/>
>返回值：返回`s1`的值<br/>

### （24）toupper 函数：把小写英文字母转换成对应的大写英文字母
>**toupper**<br/>
>头文件：`#include <ctype.h>`<br/>
>格式：&emsp;`int toupper(int c);`<br/>
>功能：&emsp;把小写英文字母转换成对应的大写英文字母<br/>
>返回值：如果`c`是小写英文字母，就返回转换成大写英文字母后的值，否则直接返回`c`<br/>

### （25）tolower 函数：把大写英文字母转换成对应的小写英文字母
>**tolower**<br/>
>头文件：`#include <ctype.h>`<br/>
>格式：&emsp;`int tolower(int c);`<br/>
>功能：&emsp;把大写英文字母转换成对应的小写英文字母<br/>
>返回值：如果`c`是大写英文字母，就返回转换成小写英文字母后的值，否则直接返回`c`<br/>

### （26）getch函数：获取按下的键(windows)
>**getch**<br/>
>头文件：`#include <conio.h>`(*非标准库)<br/>
>格式：&emsp;`int getch(void);`<br/>
>功能：&emsp;直接从键盘读取字符而不回显<br/>
>返回值：返回读取到的字符的值

### （27）putch函数：输出到控制台(windows)
>**putch**<br/>
>头文件：`#include <conio.h>`(*非标准库)<br/>
>格式：&emsp;`int putch(int c);`<br/>
>功能：&emsp;在画面上显示字符`c`（在一些特殊的编程环境中，如果`c`是换行符就只换行而不进行返回操作）<br/>
>返回值：显示成功后返回输入的字符`c`,错误则返回`EOF`

liunx 使用的Curses库的概要
| | |
-|-
initscr | 生成屏幕并初始化库。使用Curses库是必须最先调用该函数
cbreak|禁止行缓冲
noecho|禁止输入的字符显示在画面上
refresh|刷新画面
getch|返回输入的字符
endwin|使用库时用最后收尾的函数。使用Curses库时必须最后调用该函数（通常情况下，画面上的字符会全部消失）

### （26）va_start 宏：访问可变参数前的准备
>**va_start**<br/>
>头文件：`#include <stdarg.h>`<br/>
>格式：&emsp;`void va_start(va_list ap,最终参数);`<br/>
>功能：&emsp;必须在访问无名称的实际参数前调用该宏。为了后续调用va_arg及va_end,需提前初始化ap。作为形式参数的最终参数是函数定义过程中位于可变形式参数列表最右边的形式参数的标识符，也就是省略符号",..."前的标识符，当作为形式参数的最终参数被声明为下列类型时，作未定义处理 □ register存储类 □ 函数类 □ 数组类 □ 与应用了默认的实际参数提升的类型不匹配的类型<br/>
>返回值：无

### (27)va_arg宏：取出可变参数
>**va_arg**<br/>
>头文件：#include <stdarg.h><br/>
>格式：&emsp;`类型va_arg(va_list ap,类型);`<br/>
>功能：在函数调用中展开为一个包含可变参数列表中下一个实际参数的值和类型的表达式。形式参数`ap`必须和通过`va_start`初始化的`va_list ap`相同。接下来调用`va_arg`时，更新`ap`以返回下一个实际参数的值。形式参数的类型名为所指定的类型名，但是必须在类型的后面加上一个后缀`*`才能获得指向该类型对象的指针类型。当没有下一个实际参数时，或者类型和实际的（随着既定的实际参数提升而被提升的）下一个实际参数的类型不匹配时，作未定义处理<br/>
>返回值：在调用va_start宏后首次调用该宏时，将返回最终参数指定的实际参数的下一个实际参数的值。后继的一连串调用将安顺序返回剩下的实际参数的值

### （28）va_end宏：结束对可变参数的访问
>**va_end**<br/>
>头文件：`#include <stdarg.h>`<br/>
>格式：&emsp;`void va_end(va_list ap);`<br/>
>功能：&emsp;结束对可变参数列表的处理，使用函数正常返回。编程环境允许`va_end`宏更新`ap`令`ap`无法使用（只要不再次调用`va_start`）。当没有调用对应的`va_start`宏时，或者没有在返回前调用`va_end`宏时，作未定义处理<br/>
>返回值：无

### （29）vprintf函数/vfprintf函数：输出到流
>**vprinf**
>头文件：`#include <stdio.h>`
		`#include <stdarg.h>` <br/>
>格式：&emsp;int vprintf(const char *format,va_list arg);<br/>
>功能：&emsp;函数等价于用`arg`替换可变实际参数列表的printf函数。调用该函数前，必须事先用`va_start`宏初始化`arg`（可以继续调用`va_arg`）。该函数不调用va_end宏<br/>
>返回值：返回写入的字符数量。发生输出错误时则返回负值

>**vfprinf**
>头文件：`#include <stdio.h>`
		`#include <stdarg.h>`<br/>
>格式：&emsp;`int vfprintf(FILE *stream,const char *format,va_list arg);`
>功能：函数等价于用`arg`