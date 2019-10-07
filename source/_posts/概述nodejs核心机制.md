---
title: 概述nodejs核心机制
tags:
  - nodejs
url: 849.html
id: 849
categories:
  - technology
date: 2018-06-10 17:17:49
---

nodejs - Non-Blocking I/O Model - Event Loop - Event-Driven - 基本架构 - 何为阻塞 - 代码执行时 - 阻止事件循环的几个维度 - Worker Pool - npm 模块的风险

# Non-Blocking I/O Model

non-blocking 是指 node.js 进程中不同步等待执行`非javascript操作`（例如 I/O）完成而继续执行下一块代码的特性。

> 注：CPU 密集型属于 javascript 操作。

I/O 通常指与`磁盘`与`网络`的交互 非阻塞 I/O 模型使得 nodejs 支持高并发且非常适合于 I/O 密集型应用

# Nodejs Event Loop and Worker Pool

共 6 个阶段

- `timers` setTimeout 与 setInterval 回调函数队列
- `pending callbacks` 会在下一次 loop 中执行的系统级回调队列。如 TCP ECONNREFUSED -`idle,prepare` 内部使用
- `poll` 接收新的 I/O 事件。执行 I/O 相关回调。在这个阶段 node 进程可能会阻塞
- `check` setImmediate 回调会在这个阶段执行
- `close` 一些关闭的回调。比如`connect.on('close', () => {....})`

> 注：process.nextTick 不属于任何一个阶段，它是介于任意两个阶段之间，并且在阶段切换时执行 nextTick 回调

# Event-Driven

### 基本架构

- nodejs 事件驱动架构中有两种线程：事件循环线程（Event Loop）以及工作线程池（worker pool）
- Event Loop 负责编排客户端请求而后调度 Worker Pool 处理 CPU 密集型任务

> 注：因此 nodejs 并不是纯粹的单线程语言！

### 何为阻塞

- 如果 Event Loop 执行回调或 worker 执行任务需要很长时间，即为阻塞。当发生阻塞时，主要会有两点需要考虑：
  - 性能：如果某 worker 线程定期执行 heavyweight 任务，会影响服务吞吐量（请求/秒）
  - 安全性：假设某些输入会引起程序阻塞，则存在被恶意客户端利用并攻击的风险。即拒绝服务攻击。

### 代码执行时

- 在 Event Loop 中`同步`执行常规的变量、方法的定义与调用，javascript 所有回调以及非阻塞异步 I/O 如网络 I/O
- Worker Pool 是 libuv（线程池工作调度的 c++库）在 Worker Pool 中`异步`执行“昂贵”繁重的任务。node 提供非阻塞 I/O（操作系统不提供）API，以及 CPU 密集的 I/O API
  - I/O 密集型 API：
  - DNS: dns.lookup()
  - fs: fs.readFile()，除了那些显示说明同步的方法
  - CPU 密集型 API：
  - crypto: crypto.pbkdf2()
  - zlib: 除了那些显示说明同步的方法

### Event Loop 实质

抽象来说，Event Loop 维护挂起事件的队列，Worker Pool 维护挂起任务的队列。 实际上，Event Loop 并不是维护一个队列。而是一个`文件描述符的集合`，这些文件描述符从系统级事件通知机制获取比如 epoll(linux),kqueue(OSX),IOCP(Windows)。这些文件描述符对应于某些网络套接字以及 node 正在监视的文件等等。当某个描述符准备好时，Event Loop 会将其转换为合适的事件并执行对应的回调。 另外，Worker Pool 维护的是一个真正的队列。Worker 会 pop 出队列的 task 并执行，完成后会触发 Event Loop“至少一个事件已完成”的事件。

### 阻止事件循环的几个维度

1.  数据处理流程中是否包含计算复杂度高的任务，比如使用 CPU 密集型 Node API 比如 crypto,fs,zlib,child-process（分区处理与 offload to Worker Pool）
2.  ReDoS 攻击，检查是否存在易受攻击的正则表达式（使用[安全正则表达式库做安全校验](https://github.com/substack/safe-regex)）
3.  是否在主线程中使用 JSON.parse 以及 JSON.stringify（潜在风险，因此也建议 offload 给 Worker Pool）

### Worker Pool

nodejs 默认的 Worker Pool 专门用于处理 I/O 任务，维护自己的线程池可以使用 cluster 模块以及 child_process 模块做自定义线程池。 Node 服务器的吞吐量`取决于`WorkerPool 的吞吐量。有效`降低逐个任务时间开销`以及`稳定任务时间开销的变化`将最大程度提升服务器的吞吐量。最常见的方法就是复杂重复型任务（比如数组迭代）做分区处理。

> 注：由于调度 Worker Pool 会增加额外的通信开销，因为 Worker Pool 无法获取主线程的命名空间从而无法直接读取 Javascript 对象，所以需要序列化/反序列化导致增加通信成本。

### npm 模块的风险

npm 生态系统中存在数十万个模块为开发者提供了极大的便利，然而社区中 npm 包良莠不齐，因为无法较为准确的估计其使用 Event Loop 或者 Worker Pool 的成本而导致一些程序隐患。
