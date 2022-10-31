# 1. 线程创建的方式

## 1. ThreadPoolExecutor

###  实现代码

```java
 public ThreadPoolExecutor(int corePoolSize,
     int maximumPoolSize,
     long keepAliveTime,
     TimeUnit unit,
     BlockingQueue<Runnable> workQueue,
     ThreadFactory threadFactory,
     RejectedExecutionHandler handler){};
```

### 参数含义

| 参数            | 作用                                                       |
| --------------- | ---------------------------------------------------------- |
| corePoolSize    | 指定线程池的线程数量（核心线程），不能小于0                |
| maximumPoolSize | 指定线程池可支持的最大线程数，最大数量>=核心线程数         |
| keepAliveTime   | 指定临时线程的最大存活时间，不能小于0                      |
| unit            | 指定临时线程存货时间的单位（秒，分，时，天）               |
| workQueue       | 指定任务队列，不能为null                                   |
| threadFactory   | 指定创建线程的线程工程，不能为null                         |
| handler         | 指定线程忙，任务队列已满时，新任务来之后的处理，不能为null |

### 新任务拒绝策略

| 策略                                     | 说明                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| ThreadPoolExecutor.AbortPolicy()         | 丢弃任务并抛出RejectedExecutionException异常。`默认的策略`   |
| ThreadPoolExecutor.DiscardPolicy()       | 丢弃任务但是不抛出异常。`不推荐`                             |
| ThreadPoolExecutor.DiscardOldestPolicy() | 抛弃队列中等待最久的任务，然后把当前的新任务加入队列中。     |
| ThreadPoolExecutor.CallerRunPolicy()     | 由主线程负责调用任务的run()方法从而绕过线程池直接执行`main线程执行` |

### 常见问题

#### 临时线程何时创建？

> 新任务提交时核心线程都被占用，任务队列已满，且还可以创建临时线程时（最大线程数 - 核心线程 > 0）,才会创建临时线程

#### 什么时候会开始拒绝任务？

> 核心线程与临时线程都被占用且任务队列已满时，新任务进入会被拒绝。

## 2. Executors

