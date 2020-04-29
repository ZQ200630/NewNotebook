### 陈述式声明管理

```shell
kubectl get namespace #访问名称空间
kubectl get ns #简化版
kubectl get all -n default #查询default空间所有资源
kubectl create namespace app #创建名称空间
kubectl delete namespace app #删除名称空间
kubectl get pods -n namespace -o wide #扩展显示，-n后接名称空间
kubectl describe deployment app -n namespace #详细描述
kubectl exec -ti podname /bin/bash -n namespace #运行容器，和docker exec是一样的
kubectl get svc -n namespace #查看service资源
kubectl scale deployment name --replicas=2 -n namespace #扩容

kubectl get pods podsname -o yaml -n namespace #访问pods配置清单
kubectl get pods podsname -o json -n namespace #访问pods配置清单
kubectl explain service #点读机，查看清单配置

kubectl edit svc svcname # 进入文件修改即可自动修改
kubectl delete -f path.yaml #用文件删除
```

