---
title: 阿里Linux Shell 脚本面经25个经典问答
tags: temp
notebook: temp
---
## 阿里Linux Shell 脚本面经25个经典问答 
#### Q:1 Shell脚本是什么，它是必需的吗？ 
答：一个Shell脚本是一个文本文件，包含一个或多个命令。作为系统管理员，我们经常需要使用多个命令来完成一项任务，我们可以添加这些所有命令在一个文本文件（Shell脚本）来完成这些日常工作任务。 
#### Q:2 什么是默认登录Shell，如何改变指定用户的登录Shell 
答：在Linux操作系统，`/bin/bash` 是默认登录shell，是在创建用户时分配的。使用chsh命令可以改变默认的shell。示例如下所示：
``` Shell Script
            # chsh <username> -s <new_default_shell>
            # chsh linuxtechi -s  /bin/sh            
```
