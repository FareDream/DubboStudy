Dubbo
=
* Dubbo支持的集群容错模式
    * Failover Cluster模式：
        * 配置值为failover。这种模式是Dubbo集群容错默认的模式选择，调用失败时，会自动切换，重新尝试调用其他节点上可用的服务。对于一些幂等性操作可以使用该模式，如读操作，因为每次调用的副作用是相同的，所以可以选择自动切换并重试调用，对调用者完全透明。可以看到，如果重试调用必然会带来响应端的延迟，如果出现大量的重试调用，可能说明我们的服务提供方发布的服务有问题，如网络延迟严重、硬件设备需要升级、程序算法非常耗时，等等，这就需要仔细检测排查了。
          例如，可以这样显式指定Failover模式，或者不配置则默认开启Failover模式，配置示例如下：  
        ```
        <dubbo:service interface="org.shirdrn.dubbo.api.ChatRoomOnlineUserCounterService" version="1.0.0"
             cluster="failover" retries="2" timeout="100" ref="chatRoomOnlineUserCounterService" protocol="dubbo" >
             <dubbo:method name="queryRoomUserCount" timeout="80" retries="2" />
        </dubbo:service>
        ```
    * Failfast Cluster模式
        * 配置值为failfast。这种模式称为快速失败模式，调用只执行一次，失败则立即报错。这种模式适用于非幂等性操作，每次调用的副作用是不同的，如写操作，比如交易系统我们要下订单，如果一次失败就应该让它失败，通常由服务消费方控制是否重新发起下订单操作请求（另一个新的订单）。
    * Failsafe Cluster模式
        * 配置值为failsafe。失败安全模式，如果调用失败， 则直接忽略失败的调用，而是要记录下失败的调用到日志文件，以便后续审计。
    * Failback Cluster模式
        * 配置值为failback。失败自动恢复，后台记录失败请求，定时重发。通常用于消息通知操作。
    * Forking Cluster模式
        * 配置值为forking。并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。
    * Broadcast Cluster模式
        * 配置值为broadcast。广播调用所有提供者，逐个调用，任意一台报错则报错（2.1.0开始支持）。通常用于通知所有提供者更新缓存或日志等本地资源信息。
         