---
title: IDEA的一些DEBUG调试技巧
published: 2024-07-16
description: "一些IDEA的DEBUG调试技巧"
image: "https://cdn.jsdelivr.net/gh/Flandre923/CDN@latest/img/20240716210357.png"
tags: ["IDEA"]
category: IDEA
draft: false
---

# IDEA调试Java
DEBUG模式


## 1. 行断点
直接在代码的一行的开始位置的数字前面点击下，即可设置断点，并且通过右键还可以给这个断点设置出发的条件，例如在循环中，可以设置为当循环满足一定的条件时候出发断点。

进入DEBUG模式一些快捷键
WIN+F10 如果你在浏览器他的文件或其他的位置，按这个可以快速回到当前的断点位置
F8 step over 当前文件夹中的代码，一行一行执行，不会进入具体方法的内部。
F7 step into 运行当前的代码，如果遇到了方法就会进入到方法的内部。
alt+shift+f7 force step into 当默认的step into会在当前的环境中运行，不回进入到默认的一些库的实现中，此时的force step into 可以进入其中
Shift+F8 step out 跳出当前的方法回上一个方法。
alt+F9 run to cursor 运行到当前光标

Evaluate Expression 输入栏可以输入表达式并返回结果。
Edit run configuration projectname  这里可以修改你的项目的debug的运行的一些参数吗，例如追加一些运行时候启动的参数，追加JVM参数等。

F9 resume program 回复程序继续执行。
ctrl+F2 stop program 停止程序。

ctrl+shift+F8 view breakpoints 查看设置的断点，以及是否启动exception报错自动设置断点。你也可以设置自己需要停止的exception位置.

mute breakpoints 是将所有断点关闭

debugger小窗口的左边是线程所对应的调用堆栈.你在这里可以看到什么方法调用你的方法.右边的窗口是当前的环境中的变量的数值.我们在变量去可以通过右键添加watch的方法添加一个对新的变量查看数值的方法.

## 2. view breakpoints

点击view breakpoints的界面的中,我们来看看其中的一些含义
enable 是否启用这个断点
/+ 新增一个断点
/- 删去一个断点
Suspend Threads 是否暂停线程，当设置为true的时候，当断点触发的时候，其他线程也会暂停。
Suspend All Threads 是否暂停所有线程，当设置为true的时候，当断点触发的时候，所有线程都会暂停。  

Condition 条件 你可以在这里设置断点出发的情况,例如在循环中，可以设置为当循环满足一定的条件时候出发断点。
Log 勾上可以将break hit 和 message 打印到控制台。
remove once hit 一次断点.

## 3. 条件断点

之前在view breakpoints中我们讲了条件断点的是设置方法, 这里也有一些其他的设置方法,在设置断点后右键可以输入条件,更加方便的设置条件断点.

## 4. stream breakpoint

你可以在stream的流调用中打断点,此时debug中会有一个Trace Current Stream Chain的方法,你可以通过这个方法查看当前的stream都做了什么操作.(貌似社区版没有这个功能)

## 5. force return

如果你发现了一个bug,你不向执行下面的代码,可以使用force return的方法返回当前函数,就会在当前调试的停止的地方直接返回,如果有返回值你可以设置返回值.

## 6. 多线程
对断点右键可以设置为Treand,默认是all,即停止所有线程,而thread可以按照线程停止,并切换到对应的线程中去.

## 7. 方法断点

打在方法上,方法进入和方法退出时候回调用这个断点,并且这个断点可以打在接口上,当执行接口的时候就会触发这个断点,位置是具体的实现类.

## 9. 字段断点

直接将断点打在属性上,此时如果改变这个属性的数值时候就会触发这个断点程序停止执行.你可以右键设置是否字段访问也触发这个断点.

## 10. 异常断点

可以自己设置异常,触发时候会停止程序.
