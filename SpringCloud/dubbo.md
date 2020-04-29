### Dubbo项目配置
- Dubbo-Provider

- ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
  
    <parent>
        <groupId>com.zq</groupId>
        <artifactId>mavendependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>
  
    <packaging>pom</packaging>
    <artifactId>provider-dubbo</artifactId>
    <name>provider-dubbo</name>
  
    <modules>
        <module>service</module>
        <module>api</module>
    </modules>
  </project>
  ```
  
- Api & Service子模块

- ```xml
  <!-- api -->
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
      <parent>
          <artifactId>provider-dubbo</artifactId>
          <groupId>com.zq</groupId>
          <version>1.0.0-SNAPSHOT</version>
      </parent>
  
      <artifactId>api</artifactId>
      <version>1.0.0-SNAPSHOT</version>
      <packaging>jar</packaging>
  
      <properties>
          <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
          <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
          <java.version>13</java.version>
      </properties>
  </project>
  ```

- ```xml
  <!-- Service -->
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
      <artifactId>service</artifactId>
      <version>1.0.0-SNAPSHOT</version>
      <packaging>jar</packaging>
  
      <name>service</name>
      <description>Demo project for Spring Boot</description>
  
      <parent>
          <groupId>com.zq</groupId>
          <artifactId>mavendependencies</artifactId>
          <version>1.0.0-SNAPSHOT</version>
      </parent>
  
      <properties>
          <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
          <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
          <java.version>13</java.version>
      </properties>
  
      <dependencies>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter</artifactId>
          </dependency>
  
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-actuator</artifactId>
          </dependency>
  
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-test</artifactId>
              <scope>test</scope>
          </dependency>
  
          <dependency>
              <groupId>org.apache.dubbo</groupId>
              <artifactId>dubbo</artifactId>
          </dependency>
  
          <!-- https://mvnrepository.com/artifact/org.apache.dubbo/dubbo-spring-boot-starter -->
          <dependency>
              <groupId>org.apache.dubbo</groupId>
              <artifactId>dubbo-spring-boot-starter</artifactId>
              <version>2.7.5</version>
          </dependency>
  
          <!-- https://mvnrepository.com/artifact/org.apache.dubbo/dubbo-registry-nacos -->
          <dependency>
              <groupId>org.apache.dubbo</groupId>
              <artifactId>dubbo-registry-nacos</artifactId>
              <version>2.7.5</version>
          </dependency>
  
          <!-- https://mvnrepository.com/artifact/com.alibaba.nacos/nacos-client -->
          <dependency>
              <groupId>com.alibaba.nacos</groupId>
              <artifactId>nacos-client</artifactId>
              <version>1.1.4</version>
          </dependency>
  
          <!-- https://mvnrepository.com/artifact/com.alibaba.spring/spring-context-support -->
          <dependency>
              <groupId>com.alibaba.spring</groupId>
              <artifactId>spring-context-support</artifactId>
              <version>1.0.6</version>
          </dependency>
  
          <dependency>
              <groupId>com.zq</groupId>
              <artifactId>api</artifactId>
              <version>1.0.0-SNAPSHOT</version>
          </dependency>
      </dependencies>
  </project>
  ```

- Api中新建一个接口

- ```java
  //Service 中写接口的实现类
  
  @Service(version="1.0.0")
  public class EchoServiceImpl implements EchoService {
      @Override
      public String echo() {
          return "Echo Hello Double";
      }
  }
  ```

- ```yaml
  #service的properties文件
  spring:
    application:
      name: Dobble-Provider
    main:
      allow-bean-definition-overriding: true
  
  dubbo:
    scan:
      base-packages: com.zq.service
    protocol:
      name: dubbo
      port: -1
    registry:
      address: nacos://192.168.31.235:8848
  ```

- Consumer

- ```java
  //Consumer的Controller,注意@Reference注解
  
  @RestController
  public class TestController {
      @Reference(version = "1.0.0")
      private EchoService service;
  
      @GetMapping("/test111")
      public String echo() {
          return service.echo();
      }
  }
  ```

- ```yaml
  # Consumer 的 properties
  server:
    port: 8765
  spring:
    main:
      allow-bean-definition-overriding: true
    application:
      name: Dobble-Provider
  
  
  dubbo:
    scan:
      base-packages: com.zq.controller
    protocol:
      name: dubbo
      port: -1
    registry:
      address: nacos://192.168.31.235:8848
  ```

- Consumer的pom.xml

- ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
      <artifactId>consumer-dubbo</artifactId>
      <version>1.0.0-SNAPSHOT</version>
      <packaging>jar</packaging>
  
      <name>consumer-dubbo</name>
      <description>Demo project for Spring Boot</description>
  
      <parent>
          <groupId>com.zq</groupId>
          <artifactId>mavendependencies</artifactId>
          <version>1.0.0-SNAPSHOT</version>
      </parent>
  
      <properties>
          <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
          <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
          <java.version>13</java.version>
      </properties>
  
      <dependencies>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-web</artifactId>
          </dependency>
  
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-actuator</artifactId>
          </dependency>
  
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-test</artifactId>
              <scope>test</scope>
          </dependency>
  
          <dependency>
              <groupId>org.apache.dubbo</groupId>
              <artifactId>dubbo</artifactId>
          </dependency>
  
          <!-- https://mvnrepository.com/artifact/org.apache.dubbo/dubbo-spring-boot-starter -->
          <dependency>
              <groupId>org.apache.dubbo</groupId>
              <artifactId>dubbo-spring-boot-starter</artifactId>
              <version>2.7.5</version>
          </dependency>
  
          <!-- https://mvnrepository.com/artifact/org.apache.dubbo/dubbo-registry-nacos -->
          <dependency>
              <groupId>org.apache.dubbo</groupId>
              <artifactId>dubbo-registry-nacos</artifactId>
              <version>2.7.5</version>
          </dependency>
  
          <!-- https://mvnrepository.com/artifact/com.alibaba.nacos/nacos-client -->
          <dependency>
              <groupId>com.alibaba.nacos</groupId>
              <artifactId>nacos-client</artifactId>
              <version>1.1.4</version>
          </dependency>
  
          <!-- https://mvnrepository.com/artifact/com.alibaba.spring/spring-context-support -->
          <dependency>
              <groupId>com.alibaba.spring</groupId>
              <artifactId>spring-context-support</artifactId>
              <version>1.0.6</version>
          </dependency>
  
          <dependency>
              <groupId>com.zq</groupId>
              <artifactId>api</artifactId>
              <version>1.0.0-SNAPSHOT</version>
          </dependency>
      </dependencies>
  </project>
  ```

### dubbo负载均衡

```yml
# yml配置
dubbo:
	provider:
		loadbalance: roundrobin  #轮询
					 random #随机
					 leastactive #最小活跃数
					 consistenthash #一致性hash
```

