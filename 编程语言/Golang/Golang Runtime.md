# Golang Runtime

### Runtime简介及历程

Golang Runtime 是 Go 语言运行所需要的基础设施，负责支撑语言的核心功能和高效执行环境。

1. 协程调度、内存分配与垃圾回收 (GC)：
   - Golang 的 runtime 提供了高效的协程（Goroutine）调度器、动态内存分配机制以及垃圾回收机制（GC）。
2. 操作系统及 CPU 相关操作封装：
   - 处理信号（Signal）、系统调用（Syscall）、寄存器操作、原子操作等，确保程序在不同操作系统和硬件架构上的一致性。
3. 工具支持：
   - 提供 `pprof`、`trace` 和 `race` 检测等工具，便于性能分析和竞争条件检测。
4. 内置类型和反射实现：
   - 实现 `map`、`channel`、`string` 等基础类型的逻辑，以及 `reflect` 包的动态类型功能。

一些 Go 的关键字会在编译时直接映射到 runtime 包下的函数。例如：

| **关键字** | **对应 runtime 函数**                 |
| ---------- | ------------------------------------- |
| `go`       | `newproc`                             |
| `new`      | `newobject`                           |
| `make`     | `makeslice`, `makechan`, `makemap` 等 |
| `gc`       | `gcStart`                             |
| `chan`     | `chansend1`, `chanrecv1`              |

------

### 调度 Goroutine

Golang 的调度模型体现了轻量级线程的理念，通过高效的协程机制实现了对系统资源的充分利用。

#### **调度模型概览**

1. **线程关系：**
   - **Process (进程)** -> **Thread (LWP, Lightweight Process)** -> **Goroutine (轻量级用户态线程)**
   - Goroutine 通过复用系统线程降低上下文切换成本，提高了并发性能。
2. **GPM 模型：**
   - G (Goroutine): 表示 Goroutine 实例，包含执行函数及其上下文。
   - P (Processor): 逻辑处理器，负责将 G 分配给 M 执行。
   - M (Machine): 表示内核线程，实际负责运行 G。
3. **核心特点：**
   - **有栈协程：** Goroutine 使用动态增长和缩小的栈，初始栈仅为 2KB，避免了固定栈的资源浪费。
   - **抢占式调度：** runtime 支持抢占式调度，避免 Goroutine 长时间占用 CPU。
   - **工作窃取：** P 会从其他 P 的队列中窃取 Goroutine 执行，提升多核利用率。

#### **调度机制的优化过程**

- 初始版本使用简单的全局队列。
- 随后引入了本地队列和全局队列相结合的模型，提高了并发性能。
- 支持 M 动态调整数量，匹配实际负载。

------

### 内存与 GC

Golang runtime 提供了高效的内存管理机制，包括动态内存分配、垃圾回收等。

#### **内存管理**

1. 内存分配：

   - runtime 提供了 

     ```
     mallocgc
     ```

      函数用于内存分配，并根据对象大小选择不同的分配策略：

     - 小对象分配在 Thread Local Cache（TLC）。
     - 大对象直接从堆分配。

2. 栈管理：

   - Goroutine 的栈可以动态扩展和收缩，避免固定栈带来的资源浪费。

#### **垃圾回收（GC）**

1. **GC 类型：**
   - Go 使用并发标记-清除垃圾回收器（Concurrent Mark-and-Sweep）。
2. **GC 特性：**
   - **STW（Stop-The-World）：** GC 开始时会暂停所有 Goroutine。
   - 三色标记法：
     - 对象分为白、灰、黑三种状态，标记过程中逐步将存活对象移到黑色。
   - **增量式回收：** 减少 STW 时间，提升应用程序响应能力。
3. **GC 调优：**
   - GOGC 环境变量用于设置 GC 的触发频率，默认值为 100（表示 100% 的堆增长触发 GC）。
   - 通过 `runtime.GC()` 强制触发 GC。

------

### 实践

1. **性能调试：**

   - 使用 

     ```
     pprof
     ```

      分析 CPU 和内存使用：

     ```bash
     go tool pprof <binary> <profile>
     ```

   - 使用 `trace` 生成调试跟踪文件。

2. **竞争条件检测：**

   - 在运行时启用竞争条件检测：

     ```bash
     go run -race main.go
     ```

3. **控制 Goroutine：**

   - 使用 `runtime.Gosched()` 手动让出线程。

   - 设置最大并发线程数：

     ```go
     runtime.GOMAXPROCS(n)
     ```

4. **GC 调试：**

   - 打印 GC 日志：

     ```bash
     GODEBUG=gctrace=1 ./program
     ```