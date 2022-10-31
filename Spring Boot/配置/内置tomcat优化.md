spring-configuration-metadata.json内置tomcat优化
1、常用配置说明
我们在使用springboot开发web项目时，大多数时候采用的是内置的Tomcat（当然也可以配置支持内置的jetty），内置Tomcat有什么好处呢？

方便微服务部署，减少繁杂的配置
方便项目启动，不需要单独下载web容器，如Tomcat，jetty等。

针对目前的容器优化，可以从以下几点考虑：

1、线程数  2、超时时间  3、JVM优化

首先，线程数是一个重点，每一次HTTP请求到达Web服务器，Web服务器都会创建一个线程来处理该请求，该参数决定了应用服务同时可以处理多少个HTTP请求。

比较重要的有两个：初始线程数和最大线程数。

初始线程数：保障启动的时候，如果有大量用户访问，能够很稳定的接受请求。
最大线程数：用来保证系统的稳定性。
超时时间：用来保障连接数不容易被压垮。如果大批量的请求过来，延迟比较高，很容易把线程数用光，这时就需要提高超时时间。这种情况在生产中是比较常见的 ，一旦网络不稳定，宁愿丢包也不能把服务器压垮。
min-spare-threads：最小备用线程数，tomcat启动时的初始化的线程数。默认10

max-threads：Tomcat可创建的最大的线程数，每一个线程处理一个请求，超过这个请求数后，客户端请求只能排队，等有线程释放才能处理。（建议这个配置数可以在服务器CUP核心数的200~250倍之间）默认200

accept-count：当调用Web服务的HTTP请求数达到tomcat的最大线程数时，还有新的HTTP请求到来，这时tomcat会将该请求放在等待队列中，这个acceptCount就是指能够接受的最大等待数，默认100。如果等待队列也被放满了，这个时候再来新的请求就会被tomcat拒绝（connection refused）。

max-connections：这个参数是指在同一时间，tomcat能够接受的最大连接数。一般这个值要大于(max-threads)+(accept-count)。（最大线程数+排队数）

connection-timeout：最长等待时间，如果没有数据进来，等待一段时间后断开连接，释放线程。



**注：**以上是4核8G服务器的经验配置，合适的配置还需要根据具体的压测结果来验证

```
server:
  port: 8080
  tomcat:
    uri-encoding: UTF-8
    threads:
      min-spare: 100
      max: 800
    accept-count: 1000
    max-connections: 10000
```

