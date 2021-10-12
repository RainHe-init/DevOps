### Eureka组件
#### 1、Eureka Server（理解为：汽车中介，服务提供商）
```yaml
Eureka Server 提供服务注册中心，各个节点启动后，会将自己的 IP 和端口等网络信息注册到 Eureka Server 中，
这样 Eureka Server 服务注册表中将会存储所有可用服务节点的信息，
在 Eureka 的图形化界 面可以看到所有注册的节点信息。
```
#### 2、Eureka Client（理解为：买车的人，卖车的人）
```yaml
Eureka Client 是一个 java 客户端，在应用启动后，Eureka 客户端将会向 Eureka Server 端发送心跳， 
默认周期是 30s，如果 Eureka Server 在多个心跳周期内没有接收到某个节点的心跳，
Eureka Server 将 会从服务注册表中把这个服务节点移除(默认 90 秒)。
```
#### 3、Eureka Client 分为两个角色，分别是 Application Service 和 Application Client
```yaml
卖车的人：Application Service 是服务提供方，是注册到 Eureka Server 中的服务。
买车的人：Application Client 是服务消费方，通过 Eureka Server 发现其他服务并消费。
```
#### 4、Eureka拓扑图
![image](https://github.com/498946975/DevOps/blob/master/images/springcloud16.png)
#### 5、官方名词解析
```yaml
Register(服务注册):当 Eureka 客户端向 Eureka Server 注册时，会把自己的 IP、端口、运行状况等信 息注册给 Eureka Server。
Renew(服务续约):Eureka 客户端会每隔 30s 发送一次心跳来续约，通过续约来告诉 Eureka Server 自己正常，没有出现问题。正常情况下，如果 Eureka Server 在 90 秒没有收到 Eureka 客户的续约，它会将实例从其注册表中删除。
Cancel(服务下线):Eureka 客户端在程序关闭时向 Eureka 服务器发送取消请求。 发送请求后，该客户端实例信息将从服务器的实例注册表中删除，防止 consumer 调用到不存在的服务。该下线请求不会自 动完成，它需要调用以下内容:DiscoveryManager.getInstance().shutdownComponent();
Get Registry(获取服务注册列表):获取其他服务列表。
Replicate(集群中数据同步):eureka 集群中的数据复制与同步。 Make Remote Call(远程调用):完成服务的远程调用。
```