vi /data/dockerfile/Dockerfile

```shell
FROM harbor.od.com/nginx:latest #基准镜像
USER nginx #使用镜像的用户
WORKDIR #run时当前的目录

ADD index.html /usr/share/nginx/html/index.html #将本地文件放到目录下
EXPOSE 80 #暴露的端口，没什么卵用，主要做标记

ENV VER 9.9.4-4 #创建环境变量
RUN yum install bind-$VER -y #执行命令 

RUN yum install httpd -y
CMD ["ls", "/opt"]
#CMD只能有一个，RUN可以多个

ENTRYPOINT #一开始可以执行/entrypoint脚本
```

