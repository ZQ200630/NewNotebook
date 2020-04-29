### 服务端单机注册
```properties
#服务注册中心端口号

server.port=1110

#服务注册中心实例的主机名

eureka.instance.hostname=localhost

#是否向服务注册中心注册自己

eureka.client.register-with-eureka=false

#是否检索服务

eureka.client.fetch-registry=false

#服务注册中心的配置内容，指定服务注册中心的位置

eureka.client.serviceUrl.defaultZone=http://${eureka.instance.hostname}:${server.port}/eureka/
```

### 服务端联机注册
```properties
# 应用名称
spring.application.name=eureka-register

# 服务的端口号，若在同一台服务器上配置伪集群，则注意端口不能一样
server.port=8761

# eureka的host
eureka.instance.hostname=localhost

# 该Eureka Server是否将自己注册到注册中心
eureka.client.registerWithEureka=true

# 是否从eureka上获取注册信息
eureka.client.fetchRegistry=true

# 将Eureka自身注册到哪台Eureka服务器上，多个直接使用逗号间隔
eureka.client.serviceUrl.defaultZone=http://${eureka.instance.hostname}:8760/eureka/
```

### 客户端注册
```properties
server.port=8079

spring.application.name=Producer

eureka.port=8760

eureka.instance.hostname=localhost

eureka.instance.prefer-ip-address=true

eureka.instance.instance-id=${spring.application.name}:${spring.cloud.client.ip-address}:${server.port}


#在此指定服务注册中心地址

eureka.client.serviceUrl.defaultZone=http://${eureka.instance.hostname}:${eureka.port}/eureka/
```

### 客户端调用

```java
private EurekaClient client;
InstanceInfo producer = client.getNextServerFromEureka("Producer", false);
String ipAddr = producer.getIPAddr();
String homePageUrl = producer.getHomePageUrl();


private LoadBalancerClient cli;
ServiceInstance service = cli.choose("Producer");
service.getPort();
service.getHost();
service.getServiceId()
```

