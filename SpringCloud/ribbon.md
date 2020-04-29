### 注入RestTemplate

```java
@Bean
@LoadBalanced
public RestTemplate restTemplate() {
    return new RestTemplate();
}
```

### Controller引用RestTemplate

```java
@Autowired
private RestTemplate restTemplate;
  
this.restTemplate.getForObject("http://ProviderName/Path/", ObjectClass.class);
```

### Ribbon配置类

```java
@Configuration
@ExcludeFromComponentScan
public class TestConfiguration {

  @Bean
  public IRule ribbonRule() {
    return new RandomRule();
  }
}

//新建除开配置类的接口
public @interface ExcludeFromComponentScan {
}

//启动类添加
@RibbonClient(name = "ProducerName", configuration = TestConfiguration.class)
@ComponentScan(excludeFilters = { @ComponentScan.Filter(type = FilterType.ANNOTATION, value = ExcludeFromComponentScan.class) })
```

### Ribbon自带负载均衡

| 策略名                    | 策略描述                                                     |
| :------------------------ | :----------------------------------------------------------- |
| BestAvailableRule         | 选择一个最小的并发请求的server                               |
| AvailabilityFilteringRule | 过滤掉那些因为一直连接失败的被标记为circuit tripped的后端server，并过滤掉那些高并发的的后端server（active connections 超过配置的阈值） |
| WeightedResponseTimeRule  | 根据相应时间分配一个weight，相应时间越长，weight越小，被选中的可能性越低。 |
| RetryRule                 | 对选定的负载均衡策略机上重试机制。                           |
| RoundRobinRule            | roundRobin方式轮询选择server                                 |
| RandomRule                | 随机选择一个server                                           |
| ZoneAvoidanceRule         | 复合判断server所在区域的性能和server的可用性选择server       |

