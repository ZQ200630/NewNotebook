### Kryo 实现高速序列化

- 在客户端均引入maven依赖

- ```xml
  <!-- https://mvnrepository.com/artifact/org.apache.dubbo/dubbo-serialization-kryo -->
  <dependency>
      <groupId>org.apache.dubbo</groupId>
      <artifactId>dubbo-serialization-kryo</artifactId>
      <version>2.7.5</version>
  </dependency>
  
  ```

- 在application.properties中配置dubbo.protocol.serialization=kryo即可

- 在domain领域对象中implements serializable

