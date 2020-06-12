# Golang Runtime

## Runtime简介及历程
Golang Runtime 是Go语言运行所需要的基础设施。
1. 协程调度，内存分配，GC；
2. 操作系统及CPU相关的操作的封装（信号处理，系统调用，寄存器操作，原子操作等），CGO；
3. pprof, trace, race 检测的支持；
4. map,channel,string 等内置类型及反射实现。

一些Go的关键字被编译器编译成runtime包下的函数
关键字 | 函数
------|------
go    | newproc
new   | newobject
make  | makeslice, makechan, makemap, makemap_small...
      | gcStart
<-  ->| chansend1, chanrecv1

## 调度 Goroutine
Process -> Thread(LWP, lightweight process) -> Goroutine (一种lightweight userspace thread)
不断共享，不断减少切换成本的过程
Go协程是有栈协程

## 内存与GC

## 实践