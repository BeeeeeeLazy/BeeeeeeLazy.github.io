---
title: rt-thread学习记录(二)menuconfig和kconfig
date: 2022-06-17 08:18:25
tags: "rt-thread"
categories: "rt-thread学习记录"
---

> 本文所述内容，基于STM32HAL库+MDK5+ENV环境搭建，开发平台为Win10和Win11。限于笔者能力，肯定会有疏漏乃至错谬之处，各位看官不吝指正。
> 

# 1. 如何通过menuconfig配置工程

在工程中有一个叫 `rtconfig.h`的头文件，在这个头文件中有很多宏定义。对于C语言来说，就可以通过宏定义+条件编译，来实现修改宏定义之后，更改编译内容的效果。

例如：

```c
#define BSP_USING_UART1

#ifndef BSP_USING_UART1
	/* 工程代码 */
#endif
```

当然，随着工程的增大，单纯用人脑去记可能会记错，所以需要有一个工具来管理这些宏定义。这个工具在这里就是menuconfig。



我在STM32L051K8U6上移植了`rt_thread`工程，在`-O2`优先级编译之后，工程的大小如下：`Program Size: Code=39008 RO-data=5664 RW-data=552 ZI-data=2768`  ,从map可得知占用了`43.75KB`的ROM空间。

而STM32L051K8 U6只有`64KB ROM + 8KB RAM`，所以我们需要配置`menuconfig`去裁剪内核的大小。

### 1.1 -->RT-Thread Kernel --> Enable debugging feature

这个配置是用于LOG的，勾选之后可以使用LOG_D、LOG_E等函数输出调试信息，我这里把它取消。

裁剪之后map的ROM占用为`Total ROM Size (Code + RO Data + RW Data)      31176 (  30.45kB)`

### 1.2 --> RT-Thread Components --> MSH:command shell

删除控制台和finish之后空间大大缩小。

`Total ROM Size (Code + RO Data + RW Data)      20984 (  20.49kB)`

# 2.通过kconfig自定义配置文件

