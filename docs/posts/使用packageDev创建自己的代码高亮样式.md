---
title: 使用packageDev创建自己的代码高亮样式
date: 2018-02-08 16:24:08
tags: #Sublime #Log Highlight #Pacakage Control
---
# 1. 安装packageDev

在sublime中，使用cmd+shift+p，打开Command palette. 输出pci打开包管理，搜索并下载packageDev.

# 2.创建样式匹配文件

不知是不是因为版本问题，我使用的packageDev->New Syntax Definition使用的是YAML语法。因为这个问题还折腾了很久。如果要使用网上讲解的那种方式进行配置，使用packageDev->New TextMate Syntax Definition。

**使用New Syntax Definition配置**  
```
%YAML 1.2
---
name: MyLog  //样式名，就是在sublime右下角选择的时候，显示的名字
file_extensions: [log] //扩展文件名
scope: source.log // source不知道干嘛的，照着写的。后面是扩展名

contexts:
  main:
    - include: strings

  strings:
    - match: '\b(DEBUG)\b'
      scope: string.other.log
```
想要高亮日志是在学习使用log4j途中，查看日志所想到的。用这个方式配置的时候提示说contexts是必须要配置的，所以折腾了很久。  

以上是我用这种方式配置的时候，得出的最间配置方式。我只匹配[DEBUG]进行测试，以后还需要进行配置，自己写正则匹配就好。

话说这种方式我还没搞明白如何让sublime加载配置，QAQ。

**使用New TextMate Syntax Definition**  
```
---
name: Log
scopeName: source.log
fileTypes: [log]
uuid: 118e67e4-d1be-4da3-aaba-1b95d1d26a36

patterns:
- match: \b(DEBUG|INFO)\b
  name: markup.italic

- match: \b(ERROR)\b
  name: invalid.deprecated

- match: \b(WARN)\b
  name: support.class
```
  
这种就是网上介绍的了，按照网上说的进行配置就好。其中在name可以使用不同的关键字？来进行样式编写。应该引用是packageDev使用的Color theme中的样式了。

这种方式编写的配置文件使用cmd+b进行convert to。然后就可以在右下角看见编写的样式了。如果自定义了多个样式，在User下选择自己编写的样式。

以上。  
PS.写这篇blog的时候发现代码样式不对，出现空行，又要折腾了- -。。