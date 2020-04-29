### SpringBoot配置

```yaml
spring:
	application:
		name: AppName

server:
	port: 8088
	servlet:
		context-path: /root  #配置springboot进入路径
		
logging:
	file: ../log/springboot.log
	level.org.springframework.wen: DEBUG
```



