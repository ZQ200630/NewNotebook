### DashBoard

```shell
java -Dserver.port=8666 -Dcsp.sentinel.dashboard.server=localhost:8666 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard.jar
```

### Client Maven依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    <version>2.1.1.RELEASE</version>
</dependency>
```

### 使用流程

- ```properties
  #在原有基础上加以下两条
  spring.cloud.sentinel.transport.dashboard=localhost:8666
  feign.sentinel.enabled=true
  ```

- 在原有service包下新建fallback包

- ```java
  class FallbackService implements EchoService  //新建类遵循原有的Feigh接口，并使用@Component注解
  ```

- ```java
  @FeignClient(name = "service-provider", fallback = FallbackService.class)
  //原有Feigh接口上加fallback选项
  ```