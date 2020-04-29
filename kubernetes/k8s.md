### 虚拟机

| master | 192.168.188.20 |
| ------ | -------------- |
| node1  | 192.168.188.30 |
| node2  | 192.168.188.31 |



![QQ截图20200322150639](k8s.assets/QQ%E6%88%AA%E5%9B%BE20200322150639.png)

### 修改主机名

hostnamectl set-hostname <主机名>



### 更改网络

/etc/sysconfig/network-scripts/ifcfg-ens..



### 刷新网络

systemctl restart network.service



### 部署k8s

- ```shell
  swapoff -a  #关闭交换空间
  ```

- ```shell
  echo "[kubernetes]
  name=Kubernetes
  baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
  enabled=1
  gpgcheck=1
  repo_gpgcheck=1=
  gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg" >>/etc/yum.repos.d/kubernetes.repo
  #创建kubenetes yum源
  ```

- ```shell
  yum install --enablerepo=kubernetes kubernetes -y
  yum install --enablerepo=kubernetes etcd -y
  yum install --enablerepo=kubernetes docker cadvisor -y
  #安装docker,etcd,kubernetes
  ```

- ```shell
  echo "192.168.3.61  centos-master
  192.168.3.71  centos-minion" >>/etc/hosts
  # 在所有主机的/etc/hosts文件中加入master和node节点
  ```

- ```shell
  # How the controller-manager, scheduler, and proxy find the apiserver
  KUBE_MASTER="--master=http://centos-master:8080"
  
  # Comma separated list of nodes in the etcd cluster
  #KUBE_ETCD_SERVERS="--etcd_servers=http://centos-master:4001"
  
  # logging to stderr means we get it in the systemd journal
  KUBE_LOGTOSTDERR="-logtostderr=true"
  
  # Should this cluster be allowed to run privileged docker containers
  KUBE_ALLOW_PRIV="-allow_privileged=false"
  #编辑/etc/kubernetes/config文件
  ```






### 整个流程

1. 修改ip地址和网关
2. 更改主机名
3. 使用getenforce命令查看状态，需要为disable(在/etc/selinux/config下，修改selinux为disabled)

4. 查看uname -a，需要内核在3.8以上

5. systemctl stop firewalld 关闭防火墙

6. 配置epel源和yum源（阿里云）

   ```shell
   cd /etc/yum.repos.d/
   mkdir repo_bak
   mv *.repo repo_bak/
   ls
   
   curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
   curl -o /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
   yum clean all
   yum makecache
   
   yum list | grep epel-release
   yum install -y epel-release
   ls
   
   yum clean all 
   yum makecache 
   yum repolist enabled 
   yum repolist all
   
   #同步时间
   yum install ntpdate
   ntpdate ntp.api.bz
   ```
   
7. yum install wget net-tools telnet tree nmap sysstat lrzsz dos2unix bind-utils -y  装必要工具

8. yum install bind -y  (在其中一台主机上装（hdss7-11）)

9. 配置bind的配置文件(上述一样，一台机子上) vi /etc/named.conf

   ```js
   listen-on port 53 {本机ip;}; //更改监听地址，避免只有本机使用
   删除ipv6  //用不上
   更改allow-query -> localhost改为any //更改可行域，让某一个网段都可以使用
   添加一项 forwarders { 网关地址; }; //添加上级DNS
   recursion yes;  //递归使用上级帮助查询DNS
   dnssec-enable no;
   dnssec-validation no; //用不上，可以关闭
   ```

   10. 使用named-checkconf检查上述文件是否写错,没有报错就写对了

   11. 配置区域配置文件 /etc/named.rfc1912.zones 

       ```js
       zone "host.com" IN {
       	type master;
       	file "host.com.zone";
       	allow-update { 10.4.7.11; };
       };
       
       zone "od.com" IN {
       	type master;
       	file "od.com.zone";
       	allow-update {10.4.7.11; };
       }
       
       //该有空行有空行，该有空格有空格，该有分号有分号
       ```

       12. 配置/var/named/host.com.zone

           ```js
           $ORIGIN host.com.
           $TTL 600        ; 10 minutes
           @       IN SOA  dns.host.com. dnsadmin.host.com. (
                                           2020031801 ; serial
                                           10800      ; refresh (3 hours)
                                           900        ; retry (15 minutes)
                                           604800     ; expire (1 week)
                                           86400      ; minium (1 day)
                                           )
                                   NS   dns.host.com.
           $TTL 60 ; 1 minute
           dns             A       10.4.7.11
           HDSS7-11        A       10.4.7.11
           HDSS7-12        A       10.4.7.12
           HDSS7-21        A       10.4.7.21
           HDSS7-22        A       10.4.7.22
           HDSS7-200       A       10.4.7.200
           ```

           13. 配置/var/named/od.com.zone

           ```js
           $ORIGIN od.com.
           $TTL 600        ; 10 minutes
           @       IN SOA  dns.od.com. dnsadmin.od.com. (
                                           2020031801 ; serial
                                           10800      ; refresh (3 hours)
                                           900        ; retry (15 minutes)
                                           604800     ; expire (1 week)
                                           86400      ; minium (1 day)
                                           )
                                   NS   dns.od.com.
           $TTL 60 ; 1 minute
           dns             A       10.4.7.11
           HDSS7-11        A       10.4.7.11
           HDSS7-12        A       10.4.7.12
           HDSS7-21        A       10.4.7.21
           HDSS7-22        A       10.4.7.22
           HDSS7-200       A       10.4.7.200
           ```

           14. named-checkconf 检查有错没
           15. systemctl start named 启动域名服务
           16. netstat -tunlp | grep 53
           17. vi /etc/sysconfig/network-scripts/ifcfg-ens33 把DNS1改为更改建立的DNS服务器ip
           18. 在/etc/resolv.conf中加入search host.com可以简写域名



### 部署签发证书

```shell
#将证书安排在HDSS7-200上，即运维主机上

wget https://pkg.cfssl.org/R1.2/cfssl_linux-amd64 -O /usr/bin/cfssl

wget https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64 -O /usr/bin/cfssl-json

wget https://pkg.cfssl.org/R1.2/cfssl-certinfo_linux-amd64 -O /usr/bin/cfssl-certinfo

chmod +x /usr/bin/cfssl*

which cfssl
which cfssl-json
which cfssl-certinfo

cd /opt/
mkdir certs
cd certs

#创建CA证书签名请求的JSON配置文件
#/opt/certs/ca-csr.json
{
  "CN": "Jason",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
  	{
  		"C": "CN",
  		"ST": "beijing",
  		"L": "beijing",
  		"O": "od",
  		"OU": "ops"
  	}
  ],
  "ca": {
    "expiry": "438000h"
  }
}

#生成证书
cfssl gencert -initca ca-csr.json | cfssl-json -bare ca
#生成文件中ca为根证书，ca-key为根证书私钥

cfssl-certinfo -cert apiserver.pem
cfssl-certinfo -domain url
md5sum file
```

### 部署docker

```shell
#在HDSS7-200, HDSS7-21, HDSS7-22，即要部署k8s的节点上都装上docker
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun

#配置docker
mkdir /etc/docker
vi /etc/docker/daemon.json
#daemon.json
#下述bip中ip第三位要和主机的第四位对应
{
	"graph": "/data/docker",
	"storage-driver": "overlay2",
	"insecure-registries": ["http://harbor.od.com"],
	"registry-mirrors": ["https://kzflb.mirror.aliyuncs.com"],
	"bip": "172.7.21.1/24",
	"exec-opts": ["native.cgroupdriver=systemd"],
	"live-restore": true
}


mkdir -p /data/docker

systemctl daemon-reload
systemctl start docker
docker version
```

### 部署私有镜像仓库harbor

```shell
#harbor要1.7.6以上
#https://github.com/goharbor/harbor/releases 下载offline版本
cd /opt
mkdir src
cd src
#将源软件包放在这里,仅在运维主机安装
tar xf harbor-offline-installer-v1.9.4.tgz -C /opt
cd ..
mv harbor/ harbor-v1.9.4

ln -s /opt/harbor-v1.9.4/ /opt/harbor  #建立软连接
cd harbor #进入harbor文件夹
vi harbor.yml #编辑配置文件
hostname: harbor.od.com
http端口改成180
harbor_admin_password应该更改，默认为Harbor12345
log location改成/data/harbor/logs
data_volume: /data/harbor
#EOF

mkdir -p /data/harbor/logs

#安装docker-compose
curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version 

#启动install.sh
sh /opt/harbor/install.sh

#安装nginx
yum install -y pcre-devel openssl-devel #安装依赖包
wget http://nginx.org/download/nginx-1.10.2.tar.gz
tar xf nginx-1.10.2.tar.gz
cd nginx-1.10.2
./configure --prefix=/opt/nginx-1.10.2 --with-http_stub_status_module --with-http_ssl_module
make
make install
ln -s /usr/opt/nginx-1.10.2/ /usr/opt/nginx

#一定要关闭防火墙!!!
vi 安装目录/conf/nginx.conf
server {
        listen  80;
        server_name     harbor.od.com;
        client_max_body_size    1000m;
        location / {
                proxy_pass http://127.0.0.1:180;
        }
}

去nginx安装目录下进入sbin启动nginx

在/var/named/od.com.zone
配置harbor	A	10.4.7.200
主义serial前滚一个序号
systemctl restart named
dig -t A harbor.od.com +short
应该返回10.4.7.200
浏览器打开http://harbor.od.com
```

### 使用harbor

```shell
docker pull nginx:1.7.9
docker ps -a
docker tag containerId harbor.od.com/public/nginx
```

### 部署etcd集群

```shell
#签发证书
#HDSS7-200运维主机
/opt/certs/ca-config.json
{
    "signing": {
        "default": {
            "expiry": "438000h"
        },
        "profiles": {
            "server": {
                "expiry": "438000h",
                "usages": [
                    "signing",
                    "key encipherment",
                    "server auth"
                ]
            },
            "client": {
                "expiry": "438000h",
                "usages": [
                    "signing",
                    "key encipherment",
                    "client auth"
                ]
            },
            "peer": {
                "expiry": "438000h",
                "usages": [
                    "signing",
                    "key encipherment",
                    "server auth",
                    "client auth"
                ]
            }
        }
    }
}

#创建etcd请求文件
vi etcd-peer-csr.json

# etcd-peer-csr.json
{
    "CN": "k8s-etcd",
    "hosts": [
        "10.4.7.11",
        "10.4.7.12",
        "10.4.7.21",
        "10.4.7.22"
    ],
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "ST": "beijing",
            "L": "beijing",
            "O": "od",
            "OU": "ops"
        }
    ]
}

#签发证书
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=peer etcd-peer-csr.json |cfssl-json -bare etcd-peer

##HDSS7-12 HDSS7-21 HDSS7-22
cd /opt
mkdir src
cd src

useradd -s /sbin/nologin -M etcd
id etcd
下载etcd-v3.3.12-linux-amd64.tar.gz，放在src包下
tar xfv etcd-v3.3.12-linux-amd64.tar.gz -C /opt
cd /opt
ll
mv etcd-v3.3.12-linux-amd64 etcd-v3.3.12
ln -s /opt/etcd-v3.3.12/ /opt/etcd

mkdir -p /opt/etcd/certs /data/etcd/etcd-server /data/logs/etcd-server
#拷贝证书
cd /opt/etcd/certs
scp hdss7-200.host.com:/opt/certs/ca.pem .
scp hdss7-200.host.com:/opt/certs/etcd-peer.pem .
scp hdss7-200.host.com:/opt/certs/etcd-peer-key.pem .

#第一行，第三行，第四行，第六行需要更改
vi /opt/etcd/etcd-server-startup.sh
#!/bin/sh
/opt/etcd/etcd --name etcd-server-7-12 \
       --data-dir /data/etcd/etcd-server \
       --listen-peer-urls https://10.4.7.12:2380 \
       --listen-client-urls https://10.4.7.12:2379,http://127.0.0.1:2379 \
       --quota-backend-bytes 8000000000 \
       --initial-advertise-peer-urls https://10.4.7.12:2380 \
       --advertise-client-urls https://10.4.7.12:2379,http://127.0.0.1:2379 \
       --initial-cluster  etcd-server-7-12=https://10.4.7.12:2380,etcd-server-7-21=https://10.4.7.21:2380,etcd-server-7-22=https://10.4.7.22:2380 \
       --ca-file /opt/etcd/certs/ca.pem \
       --cert-file /opt/etcd/certs/etcd-peer.pem \
       --key-file /opt/etcd/certs/etcd-peer-key.pem \
       --client-cert-auth  \
       --trusted-ca-file /opt/etcd/certs/ca.pem \
       --peer-ca-file /opt/etcd/certs/ca.pem \
       --peer-cert-file /opt/etcd/certs/etcd-peer.pem \
       --peer-key-file /opt/etcd/certs/etcd-peer-key.pem \
       --peer-client-cert-auth \
       --peer-trusted-ca-file /opt/etcd/certs/ca.pem \
       --log-output stdout
       
       
 # 启动
 cd /opt/etcd
 chmod +x etcd-server-startup.sh 
 chown -R etcd:etcd /opt/etcd-v3.3.12/
 ll #查看user和group是否改为etcd etcd
 chown -R etcd:etcd /data/etcd/
 chown -R etcd:etcd /data/logs/etcd-server/

# 安装supervisor
yum install supervisor
systemctl start supervisord
systemctl enable supervisord


# mkdir /etc/supervisord.d
# 第一行需要更改
vim /etc/supervisord.d/etcd-server.ini # 这里的文件名称自定义
[program:etcd-server-7-12] 
command=/opt/etcd/etcd-server-startup.sh
numprocs=1                 
directory=/opt/etcd
autorestart=true 
startsecs=30
startretries=3
exitcodes=0,2
stopsignal=QUIT
stopwaitsecs=10      
user=etcd
redirect_stderr=true
stdout_logfile = /data/logs/etcd-server/etcd.stdout.log
stdout_logfile_maxbytes=64MB
stdout_logfile_backups=4
stdout_capture_maxbytes=1MB
stdout_events_enabled=false

#echo_supervisord_conf > /etc/supervisord.conf #生成配置文件
#supervisord : 启动supervisor
#supervisorctl reload :修改完配置文件后重新启动supervisor
#supervisorctl status :查看supervisor监管的进程状态
#supervisorctl start all | 进程名 ：启动全部或某进程
#supervisorctl stop all | 进程名 ：停止全部或某进程
#supervisorctl stop all：停止进程，注：start、restart、stop都不会载入最新的配置文件。
#supervisorctl update：根据最新的配置文件，启动新配置或有改动的进程，配置没有改动的进程不会受影响而重启
#supervisorctl status

systemctl start supervisord
systemctl enable supervisord
supervisorctl update
supervisorctl reload

#一定要保证所有的文件的所有者都是etcd!!!!

#检查节点状态
cd /opt/etcd
./etcdctl cluster-health
./etcdctl member list
```

### 部署apiserver

```shell
cd /opt/src
#将kubernetes-v1.16.2-server-linux-amd64.tar.gz放到此处
tar xf kubernetes-v1.16.2-server-linux-amd64.tar.gz -C /opt
cd /opt
mv kubernetes/ kubernetes-v1.16.2
ln -s /opt/kubernetes-v1.16.2/ /opt/kubernetes
cd kubernetes
rm -rf kubernetes-src.tar.gz #删除原码
cd server
cd bin
rm -f *.tar
rm -f *_tag

#HDSS7-200运维主机
#client使用证书
#/opt/certs/client-csr.json
{
    "CN": "k8s-node",
    "hosts": [
    ],
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "ST": "beijing",
            "L": "beijing",
            "O": "od",
            "OU": "ops"
        }
    ]
}

cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=client client-csr.json |cfssl-json -bare client

#apiserver证书
#/opt/certs/apiserver-csr.json
{
    "CN": "k8s-apiserver",
    "hosts": [
    	"127.0.0.1",
    	"192.168.0.1",
    	"kubernetes.default",
    	"kubernetes.default.svc",
    	"kubernetes.default.svc.cluster",
    	"kubernetes.default.svc.cluster.local",
    	"10.4.7.10",
    	"10.4.7.21",
    	"10.4.7.22",
    	"10.4.7.23"
    ],
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "ST": "beijing",
            "L": "beijing",
            "O": "od",
            "OU": "ops"
        }
    ]
}

cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=server apiserver-csr.json |cfssl-json -bare apiserver

#HDSS7-11
cd /opt/kubernetes/server/bin
mkdir cert
cd cert
#将运维主机/opt/certs中apiserver-key.pem  apiserver.pem  ca-key.pem  ca.pem  client-key.pem  client.pem拷贝到此文件夹中
cd /opt/kubernetes/server/bin
mkdir conf
cd conf
vi audit.yaml
#日志审计文件
#/opt/kubernetes/server/bin/conf/audit.yaml

apiVersion: audit.k8s.io/v1beta1 # This is required.
kind: Policy
# Don't generate audit events for all requests in RequestReceived stage.
omitStages:
  - "RequestReceived"
rules:
  # Log pod changes at RequestResponse level
  - level: RequestResponse
    resources:
    - group: ""
      # Resource "pods" doesn't match requests to any subresource of pods,
      # which is consistent with the RBAC policy.
      resources: ["pods"]
  # Log "pods/log", "pods/status" at Metadata level
  - level: Metadata
    resources:
    - group: ""
      resources: ["pods/log", "pods/status"]

  # Don't log requests to a configmap called "controller-leader"
  - level: None
    resources:
    - group: ""
      resources: ["configmaps"]
      resourceNames: ["controller-leader"]

  # Don't log watch requests by the "system:kube-proxy" on endpoints or services
  - level: None
    users: ["system:kube-proxy"]
    verbs: ["watch"]
    resources:
    - group: "" # core API group
      resources: ["endpoints", "services"]

  # Don't log authenticated requests to certain non-resource URL paths.
  - level: None
    userGroups: ["system:authenticated"]
    nonResourceURLs:
    - "/api*" # Wildcard matching.
    - "/version"

  # Log the request body of configmap changes in kube-system.
  - level: Request
    resources:
    - group: "" # core API group
      resources: ["configmaps"]
    # This rule only applies to resources in the "kube-system" namespace.
    # The empty string "" can be used to select non-namespaced resources.
    namespaces: ["kube-system"]

  # Log configmap and secret changes in all other namespaces at the Metadata level.
  - level: Metadata
    resources:
    - group: "" # core API group
      resources: ["secrets", "configmaps"]

  # Log all other resources in core and extensions at the Request level.
  - level: Request
    resources:
    - group: "" # core API group
    - group: "extensions" # Version of group should NOT be included.

  # A catch-all rule to log all other requests at the Metadata level.
  - level: Metadata
    # Long-running requests like watches that fall under this rule will not
    # generate an audit event in RequestReceived.
    omitStages:
      - "RequestReceived"


cd /opt/kubernetes/server/bin
#apiserver启动脚本
vi kube-apiserver.sh
#!/bin/sh
./kube-apiserver \
--apiserver-count 2 \
--audit-log-path /data/logs/kubernetes/kube-apiserver/audit-log \
--audit-policy-file ./conf/audit.yaml \
--authorization-mode RBAC \
--client-ca-file ./cert/ca.pem \
--requestheader-client-ca-file ./cert/ca.pem \
--enable-admission-plugins=NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,ResourceQuota,MutatingAdmissionWebhook,ValidatingAdmissionWebhook,ResourceQuota \
--etcd-cafile ./cert/ca.pem \
--etcd-certfile ./cert/client.pem \
--etcd-keyfile ./cert/client-key.pem \
--etcd-servers https://10.4.7.12:2379, https://10.4.7.21:2379, https://10.4.7.22:2379 \
--service-account-key-file ./cert/ca-key.pem \
--service-cluster-ip-range 192.168.0.0/16 \
--service-node-port-range 3000-29999 \
--target-ram-mb=1024 \
--kubelet-client-certificate ./cert/client.pem \
--kubelet-client-key ./cert/client-key.pem \
--log-dir /data/logs/kubernetes/kube-apiserver \
--tls-cert-file ./cert/apiserver.pem \
--tls-private-key-file ./cert/apiserver-key.pem \
--v 2
       
       
 chmod +x kube-apiserver.sh 
 #创建supervisor启动文件
 vi /etc/supervisord.d/kube-apiserver.ini
 #/etc/supervisord.d/kube-apiserver.ini
[program:kube-apiserver-7-21] 
command=/opt/kubernetes/server/bin/kube-apiserver.sh
numprocs=1                 
directory=/opt/kubernetes/server/bin
autorestart=true
autostart=true
startsecs=30
startretries=3
exitcodes=0,2
stopsignal=QUIT
stopwaitsecs=10      
user=etcd
redirect_stderr=true
stdout_logfile = /data/logs/kubernetes/kube-apiserver/apiserver.stdout.log
stdout_logfile_maxbytes=64MB
stdout_logfile_backups=4
stdout_capture_maxbytes=1MB
stdout_events_enabled=false

mkdir -p /data/logs/kubernetes/kube-apiserver/
supervisorctl update
```

### 反向代理keepalive vip虚拟ip

```shell
#HDSS7-11 & HDSS7-12
yum install nginx -y
#配置nginx
#/etc/nginx/nginx.conf
#在最底下添加
stream {
upstream kube-apiserver { 
      server  10.4.7.21:6443; 
      server  10.4.7.22:6443; 
} 
  
server{ 
    listen 7443; 
    proxy_connect_timeout 2s;
    proxy_timeout 900s;
    proxy_pass kube-apiserver;
        }
}


nginx -t #检查配置
systemctl start nginx
systemctl enable nginx

#配置vip
yum install keepalived -y

vi /etc/keepalived/check_port.sh

#/etc/keepalived/check_port.sh

#!/bin/bash
CHK_PORT=$1
if [ -n "$CHK_PORT" ];then
	PORT_PROCESS=`ss -lnt|grep $CHK_PORT|wc -l`
	if [ $PORT_PROCESS -eq 0 ];then
		echo "Port $CHK_PORT Is Not Used, End."
		exit 1
	fi
else
	echo "Check Porrt Cant Be Empty!"
fi

chmod +x /etc/keepalived/check_port.sh

#vi /etc/keepalived/keepalived.conf
#keepalived 主：
! Configuration File for keepalived

global_defs {#全局配置
   router_id 10.4.7.11 #可以使用主机名
}

vrrp_script chk_nginx {
	script "/etc/keepalived/check_port.sh 7443"
	interval 2
	weight -20
}

vrrp_instance VI_1 { #定义一个虚拟路由器，第一个
    state MASTER  #当前状态，主的
    interface ens33  #当前的vrp应用，绑定到那个网卡设备上
    virtual_router_id 251#这个虚拟ip，与其他主机要保持一至
    priority 100 #优先级，高于其他主机
    advert_int 1
    mcast_src_ip 10.4.7.11
    nopreempt
    authentication {
        auth_type PASS  #密码认证
        auth_pass 11111111   #密码为8位,不能用默认的密码
    }
    track_script {
    	chk_nginx
	}
    virtual_ipaddress {#虚拟ip地址
    	10.4.7.10
    }
}

#keepalived从
! Configuration File for keepalived

global_defs {
   router_id 10.4.7.12
}

vrrp_script chk_nginx {
	script "/etc/keepalived/check_port.sh 7443"
	interval 2
	weight -20
}

vrrp_instance VI_1 {
    state BACKUP
    interface ens33
    virtual_router_id 251
    priority 90
    advert_int 1
    mcast_src_ip 10.4.7.12
    authentication {
        auth_type PASS
        auth_pass 11111111
    }
    track_script {
    	chk_nginx
	}
    virtual_ipaddress {
    	10.4.7.10
    }
}
#两边的防火墙一定要关掉!!!
systemctl start keepalived
systemctl enable keepalived

```

### kube-controller-manager

```shell
vi /opt/kubernetes/server/bin/kube-controller-manager.sh
# /opt/kubernetes/server/bin/kube-controller-manager.sh

#!/bin/sh
./kube-controller-manager \
--cluster-cidr 172.7.0.0/16 \
--leader-elect true \
--log-dir /data/logs/kubernetes/kube-controller-manager \
--master http://127.0.0.1:8080 \
--service-account-private-key-file ./cert/ca-key.pem \
--service-cluster-ip-range 192.168.0.0/16 \
--root-ca-file ./cert/ca.pem \
--v 2

chmod +x /opt/kubernetes/server/bin/kube-controller-manager.sh
mkdir /data/logs/kubernetes/kube-controller-manager

vi /etc/supervisord.d/kube-controller-manager.ini
#/etc/supervisord.d/kube-controller-manager.ini
[program:kube-controller-manager-7-21]
command=/opt/kubernetes/server/bin/kube-controller-manager.sh
numprocs=1                                                                        
directory=/opt/kubernetes/server/bin                                              
autostart=true                                                                    
autorestart=true                                                                  
startsecs=30                                                                     
startretries=3                                                                    
exitcodes=0,2                                                                     
stopsignal=QUIT                                                                   
stopwaitsecs=10                                                                   
user=root                                                                         
redirect_stderr=true                                                              
stdout_logfile=/data/logs/kubernetes/kube-controller-manager/controller.stdout.log  
stdout_logfile_maxbytes=64MB                                                      
stdout_logfile_backups=4                                                          
stdout_capture_maxbytes=1MB                                                       
stdout_events_enabled=false              


supervisorctl update

vi /opt/kubernetes/server/bin/kube-scheduler.sh
#/opt/kubernetes/server/bin/kube-scheduler.sh

#!/bin/sh
./kube-scheduler \
--leader-elect true \
--log-dir /data/logs/kubernetes/kube-scheduler \
--master http://127.0.0.1:8080 \
--v 2

chmod +x /opt/kubernetes/server/bin/kube-scheduler.sh
mkdir -p /data/logs/kubernetes/kube-scheduler


vi /etc/supervisord.d/kube-scheduler.ini
#/etc/supervisord.d/kube-scheduler.ini
[program:kube-scheduler-7-21]
command=/opt/kubernetes/server/bin/kube-scheduler.sh                     
numprocs=1                                                               
directory=/opt/kubernetes/server/bin                                     
autostart=true                                                           
autorestart=true                                                         
startsecs=30                                                             
startretries=3                                                           
exitcodes=0,2                                                            
stopsignal=QUIT                                                          
stopwaitsecs=10                                                          
user=root                                                                
redirect_stderr=true                                                     
stdout_logfile=/data/logs/kubernetes/kube-scheduler/scheduler.stdout.log 
stdout_logfile_maxbytes=64MB                                             
stdout_logfile_backups=4                                                 
stdout_capture_maxbytes=1MB                                              
stdout_events_enabled=false  

supervisorctl update
supervisorctl reload 
supervisorctl status


#软连接cubectl
ln -s /opt/kubernetes/server/bin/kubectl /usr/bin/kubectl

#查看是否正常
kubectl get cs
```

### Kubelet部署

```shell
#HDSS7-200运维主机

vi /opt/certs/kubelet-csr.json
# /opt/certs/kubelet-csr.json
{
    "CN": "k8s-kubelet",
    "hosts": [
    "127.0.0.1",
    "10.4.7.10",
    "10.4.7.21",
    "10.4.7.22",
    "10.4.7.23",
    "10.4.7.24",
    "10.4.7.25",
    "10.4.7.26",
    "10.4.7.27",
    "10.4.7.28"
    ],
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "ST": "beijing",
            "L": "beijing",
            "O": "od",
            "OU": "ops"
        }
    ]
}
#签发证书
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=server kubelet-csr.json | cfssl-json -bare kubelet

#将运维主机上的证书复制到/opt/kubernetes-v1.16.2/server/bin/cert中

#HDSS7-21
mkdir /opt/kubernetes/server/conf
cd /opt/kubernetes/server/conf

#以下命令均直接运行

kubectl config set-cluster myk8s \
  --certificate-authority=/opt/kubernetes/server/bin/cert/ca.pem \
  --embed-certs=true \
  --server=https://10.4.7.10:7443 \
  --kubeconfig=kubelet.kubeconfig
  
  
kubectl config set-credentials k8s-node \
  --client-certificate=/opt/kubernetes/server/bin/cert/client.pem \
  --client-key=/opt/kubernetes/server/bin/cert/client-key.pem \
  --embed-certs=true \
  --kubeconfig=kubelet.kubeconfig 
  
  
kubectl config set-context myk8s-context \
  --cluster=myk8s \
  --user=k8s-node \
  --kubeconfig=kubelet.kubeconfig
  
  
kubectl config use-context myk8s-context --kubeconfig=kubelet.kubeconfig
  
  
vi k8s-node.yaml
#k8s-node.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: k8s-node
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:node
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: k8s-node
  
  
  kubectl create -f k8s-node.yaml
  #检查是否绑定完成
  kubectl get clusterrolebinding k8s-node -o yaml
  
  #将/opt/kubernetes/server/conf/kubelet.kubeconfig 拷到其它节点对应目录下
  
  #pause镜像
 docker pull kubernetes/pause
 docker tag imgId harbor.od.com/public/pause:latest
 docker push harbor.od.com/public/pause:latest
  
  #做cubernete启动脚本
  vi /opt/kubernetes/server/bin/kubelet.sh
  #/opt/kubernetes/server/bin/kubelet.sh
  #cgroup-driver 要与docker 的 daemon保持一致
  #hostname-overide需要根据情况更改
  
  #!/bin/sh
./kubelet \
  --anonymous-auth=false \
  --cgroup-driver systemd \
  --cluster-dns 192.168.0.2 \
  --cluster-domain cluster.local \
  --runtime-cgroups=/systemd/system.slice \
  --kubelet-cgroups=/systemd/system.slice \
  --fail-swap-on="false" \
  --client-ca-file ./cert/ca.pem \
  --tls-cert-file ./cert/kubelet.pem \
  --tls-private-key-file ./cert/kubelet-key.pem \
  --hostname-override hdss7-21.host.com \
  --image-gc-high-threshold 20 \
  --image-gc-low-threshold 10 \
  --kubeconfig ./conf/kubelet.kubeconfig \
  --log-dir /data/logs/kubernetes/kube-kubelet \
  --pod-infra-container-image harbor.od.com/public/pause:latest \
  --root-dir /data/kubelet
  
  chmod +x /opt/kubernetes/server/bin/kubelet.sh
  mkdir -p /data/logs/kubernetes/kube-kubelet /data/kubelet
  
  
  
vi /etc/supervisord.d/kube-kubelet.ini
#/etc/supervisord.d/kube-kubelet.ini
[program:kube-kubelet-7-21]
command=/opt/kubernetes/server/bin/kubelet.sh     
numprocs=1                                        
directory=/opt/kubernetes/server/bin              
autostart=true                                    
autorestart=true                                
startsecs=30                                      
startretries=3                                   
exitcodes=0,2                                     
stopsignal=QUIT                                   
stopwaitsecs=10                                   
user=root                                         
redirect_stderr=true                              
stdout_logfile=/data/logs/kubernetes/kube-kubelet/kubelet.stdout.log   
stdout_logfile_maxbytes=64MB                      
stdout_logfile_backups=4                          
stdout_capture_maxbytes=1MB                      
stdout_events_enabled=false   


supervisorctl update
supervisorctl status

#启动日志tail -fn 200 /data/logs/kubernetes/kube-kubelet/kubelet.stdout.log

#注意：apiserver启动条件是所有的etcd服务器全部启动

kubectl get nodes

kubectl label node hdss7-21.host.com node-role.kubernetes.io/master=
kubectl label node hdss7-21.host.com node-role.kubernetes.io/node=
kubectl label node hdss7-22.host.com node-role.kubernetes.io/master=
kubectl label node hdss7-22.host.com node-role.kubernetes.io/node=
```

### proxy

```shell
vi /opt/certs/kube-proxy-csr.json
#/opt/certs/kube-proxy-csr.json
{
    "CN": "system:kube-proxy",
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "ST": "beijing",
            "L": "beijing",
            "O": "od",
            "OU": "ops"
        }
    ]
}


cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=client kube-proxy-csr.json |cfssl-json -bare kube-proxy-client

cd /opt/kubernetes/server/bin/cert/
scp hdss7-200:/opt/certs/kube-proxy-client-key.pem ./
scp hdss7-200:/opt/certs/kube-proxy-client.pem ./


cd /opt/kubernetes/server/bin/conf

kubectl config set-cluster myk8s \
  --certificate-authority=/opt/kubernetes/server/bin/cert/ca.pem \
  --embed-certs=true \
  --server=https://10.4.7.10:7443 \
  --kubeconfig=kube-proxy.kubeconfig
  
  kubectl config set-credentials kube-proxy \
  --client-certificate=/opt/kubernetes/server/bin/cert/kube-proxy-client.pem \
  --client-key=/opt/kubernetes/server/bin/cert/kube-proxy-client-key.pem \
  --embed-certs=true \
  --kubeconfig=kube-proxy.kubeconfig
  
  kubectl config set-context myk8s-context \
  --cluster=myk8s \
  --user=kube-proxy \
  --kubeconfig=kube-proxy.kubeconfig
  
  kubectl config use-context myk8s-context --kubeconfig=kube-proxy.kubeconfig
  
  #把kube-proxy.kubeconfig拷贝到其它主机对应文件夹下
  
  
 vi /root/ipvs.sh

#!/bin/bash
ipvs_mods_dir="/usr/lib/modules/$(uname -r)/kernel/net/netfilter/ipvs"
for i in $(ls $ipvs_mods_dir|grep -o "^[^.]*")
do
  /sbin/modinfo -F filename $i &>/dev/null
  if [ $? -eq 0 ];then
    /sbin/modprobe $i
  fi
done

chmod +x /root/ipvs.sh
sh /root/ipvs.sh
lsmod |grep ip_vs

#创建启动文件
vi /opt/kubernetes/server/bin/kube-proxy.sh
#/opt/kubernetes/server/bin/kube-proxy.sh
#!/bin/sh
./kube-proxy \
  --cluster-cidr 172.7.0.0/16 \
  --hostname-override hdss7-21.host.com \
  --proxy-mode=ipvs \
  --ipvs-scheduler=nq \
  --kubeconfig ./conf/kube-proxy.kubeconfig
  
  
  chmod +x /opt/kubernetes/server/bin/kube-proxy.sh
  mkdir -p /data/logs/kubernetes/kube-proxy
  
  
  vi /etc/supervisord.d/kube-proxy.ini
  #/etc/supervisord.d/kube-proxy.ini
[program:kube-proxy-7-21]
command=/opt/kubernetes/server/bin/kube-proxy.sh                     
numprocs=1                                                           
directory=/opt/kubernetes/server/bin                                 
autostart=true                                                       
autorestart=true                                                     
startsecs=30                                                         
startretries=3                                                       
exitcodes=0,2                                                        
stopsignal=QUIT                                                      
stopwaitsecs=10                                                      
user=root                                                            
redirect_stderr=true                                                 
stdout_logfile=/data/logs/kubernetes/kube-proxy/proxy.stdout.log     
stdout_logfile_maxbytes=64MB                                         
stdout_logfile_backups=4                                             
stdout_capture_maxbytes=1MB                                          
stdout_events_enabled=false         


supervisorctl update
#tail -fn 200 /data/logs/kubernetes/kube-proxy/proxy.stdout.log

yum install ipvsadm -y
ipvsadm -Ln

kubectl get svc
```

### 验证集群是否正常

```
vi nginx-ds.yaml


apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  selector:
    app: mysql
  ports:
  - protocol: "TCP"
    port: 3306
    targetPort: 3306
  type: LoadBalancer
---


apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  selector:
    matchLabels:
      app: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - image: harbor.od.com/public/mysql
        name: mysql
        env:
          # Use secret in real usage
        - name: MYSQL_ROOT_PASSWORD
          value: password
        ports:
        - containerPort: 3306
          name: mysql
        
      
      
kubectl -n k8s-ecoysystem-apps create secret docker-registry registry-key \
--docker-server=harbor.od.com \
--docker-username=admin \
--docker-password=Harbor12345 \
--docker-email=2814220624@qq.com


```

### Flanneld部署

```shell
# /opt/src 把flannel压缩包放里面
mkdir /opt/flannel-v0.11.0
tar xf flannel-v0.11.0-linux-amd64.tar.gz -C /opt/flannel-v0.11.0/
ln -s /opt/flannel-v0.11.0/ /opt/flannel
cd /opt/flannel
mkdir cert/
#  把ca证书， client证书考过去

#第二行主义更改
# /opt/flannel/subnet.env
FLANNEL_NETWORK=172.7.0.0/16
FLANNEL_SUBNET=172.7.21.1/24
FLANNEL_MTU=1500
FLANNEL_IPMASQ=false

# /opt/flannel/flanneld.sh

#!/bin/sh
./flanneld \
  --public-ip=10.4.7.21 \
  --etcd-endpoints=https://10.4.7.12:2379,https://10.4.7.21:2379,https://10.4.7.22:2379 \
  --etcd-keyfile=./cert/client-key.pem \
  --etcd-certfile=./cert/client.pem \
  --etcd-cafile=./cert/ca.pem \
  --iface=ens33 \
  --subnet-file=./subnet.env \
  --healthz-port=2401
  
chmod +x /opt/flannel/flanneld.sh
mkdir -p /data/logs/flanneld

#etcd网络配置
#只需要一台etcd搞好即可
cd /opt/etcd
./etcdctl set /coreos.com/network/config '{"Network": "172.7.0.0/16", "Backend": {"Type": "host-gw"}}'

vi /etc/supervisord.d/flannel.ini

[program:flanneld-7-21]
command=/opt/flannel/flanneld.sh                   
numprocs=1                                                           
directory=/opt/flannel                       
autostart=true                                                       
autorestart=true                                                     
startsecs=30                                                     
startretries=3                                                       
exitcodes=0,2                                                        
stopsignal=QUIT                                                      
stopwaitsecs=10                                                      
user=root                                                            
redirect_stderr=true                                               
stdout_logfile=/data/logs/flanneld/flanneld.stderr.log
stdout_logfile_maxbytes=64MB                                         
stdout_logfile_backups=4                                             
stdout_capture_maxbytes=1MB                                          
stdout_events_enabled=false


supervisorctl update



### 更改模式
### ./etcdctl rm /coreos.com/network/config
### ./etcdctl set /coreos.com/network/config '{"Network": "172.7.0.0/16", "Backend": {"Type": "VxLAN"}}'
### ./etcdctl set /coreos.com/network/config '{"Network": "172.7.0.0/16", "Backend": {"Type": "VxLAN", "Directrouting":true}}'


优化：
yum install iptables-services -y
systemctl start iptables
systemctl enable iptables
iptables-save |grep -i postrouting
iptables -t nat -D POSTROUTING -s 172.7.21.0/24 ! -o docker0 -j MASQUERADE
iptables -t nat -I POSTROUTING -s 172.7.21.0/24 ! -o docker0 ! -d 172.7.0.0/16 -j MASQUERADE
iptables-save | grep -i reject
iptables -t filter -D INPUT -j REJECT --reject-with icmp-host-prohibited
iptables -t filter -D FORWARD -j REJECT --reject-with icmp-host-prohibited
iptables-save | grep -i reject
iptables-save > /etc/sysconfig/iptables

kubectl logs -f podname
查看出网ip

```

### CoreDNS

```shell
vi /etc/nginx/conf.d/k8s-yaml.od.com.conf
# /etc/nginx/conf.d/k8s-yaml.od.com.conf
server {
	listen	80;
	server_name	k8s-yaml.od.com;
	
	location / {
		autoindex on;
		default_type text/plain;
		root /data/k8s-yaml;
	}
}

mkdir /data/k8s-yaml

#HDSS7-11
vi /var/named/od.com.zone
前滚序列号
添加k8s-yaml A 10.4.7.200

#HDSS7-200
cd /data/k8s-yaml
mkdir coredns

docker pull coredns/coredns:1.6.1
docker tag containerid docker harbor.od.com/public/coredns:v1.6.1
docker push harbor.od.com/public/coredns:v1.6.1


cd /data/k8s-yaml/coredns

#rbac.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: coredns
  namespace: kube-system
  labels:
      kubernetes.io/cluster-service: "true"
      addonmanager.kubernetes.io/mode: Reconcile
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
    addonmanager.kubernetes.io/mode: Reconcile
  name: system:coredns
rules:
- apiGroups:
  - ""
  resources:
  - endpoints
  - services
  - pods
  - namespaces
  verbs:
  - list
  - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
    addonmanager.kubernetes.io/mode: EnsureExists
  name: system:coredns
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:coredns
subjects:
- kind: ServiceAccount
  name: coredns
  namespace: kube-system
  
  
#cm.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns
  namespace: kube-system
data:
  Corefile: |
    .:53 {
        errors
        log
        health
        ready
        kubernetes cluster.local 192.168.0.0/16  #service资源cluster地址
        forward . 10.4.7.11   #上级DNS地址
        cache 30
        loop
        reload
        loadbalance
       }


#dp.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: coredns
  namespace: kube-system
  labels:
    k8s-app: coredns
    kubernetes.io/name: "CoreDNS"
spec:
  replicas: 1
  selector:
    matchLabels:
      k8s-app: coredns
  template:
    metadata:
      labels:
        k8s-app: coredns
    spec:
      priorityClassName: system-cluster-critical
      serviceAccountName: coredns
      containers:
      - name: coredns
        image: harbor.od.com/public/coredns:v1.6.1
        args:
        - -conf
        - /etc/coredns/Corefile
        volumeMounts:
        - name: config-volume
          mountPath: /etc/coredns
        ports:
        - containerPort: 53
          name: dns
          protocol: UDP
        - containerPort: 53
          name: dns-tcp
          protocol: TCP
        - containerPort: 9153
          name: metrics
          protocol: TCP
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
            scheme: HTTP
          initialDelaySeconds: 60
          timeoutSeconds: 5
          successThreshold: 1
          failureThreshold: 5
      dnsPolicy: Default
      volumes:
        - name: config-volume
          configMap:
            name: coredns
            items:
            - key: Corefile
              path: Corefile
             
# svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: coredns
  namespace: kube-system
  labels:
    k8s-app: coredns
    kubernetes.io/cluster-service: "true"
    kubernetes.io/name: "CoreDNS"
spec:
  selector:
    k8s-app: coredns
  clusterIP: 192.168.0.2
  ports:
  - name: dns
    port: 53
    protocol: UDP
  - name: dns-tcp
    port: 53
  - name: metrics
    port: 9153
    protocol: TCP


kubectl create -f http://k8s-yaml.od.com/coredns/rbac.yaml
kubectl create -f http://k8s-yaml.od.com/coredns/cm.yaml
kubectl create -f http://k8s-yaml.od.com/coredns/dp.yaml
kubectl create -f http://k8s-yaml.od.com/coredns/svc.yaml

kubectl get all -n kube-system -o wide

kubectl get svc -n kube-system
#域名获取方式
#servicename.namespace.svc.cluster.local.
```

### Traefik（ingress）

```shell
#HDSS7-200
docker pull traefik:v1.7.2-alpine

#traefik-crd.yaml
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: ingressroutes.traefik.containo.us

spec:
  group: traefik.containo.us
  version: v1alpha1
  names:
    kind: IngressRoute
    plural: ingressroutes
    singular: ingressroute
  scope: Namespaced

---
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: ingressroutetcps.traefik.containo.us

spec:
  group: traefik.containo.us
  version: v1alpha1
  names:
    kind: IngressRouteTCP
    plural: ingressroutetcps
    singular: ingressroutetcp
  scope: Namespaced

---
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: middlewares.traefik.containo.us

spec:
  group: traefik.containo.us
  version: v1alpha1
  names:
    kind: Middleware
    plural: middlewares
    singular: middleware
  scope: Namespaced

---
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: tlsoptions.traefik.containo.us

spec:
  group: traefik.containo.us
  version: v1alpha1
  names:
    kind: TLSOption
    plural: tlsoptions
    singular: tlsoption
  scope: Namespaced

---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: traefik-ingress-controller

rules:
  - apiGroups:
      - ""
    resources:
      - services
      - endpoints
      - secrets
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - extensions
    resources:
      - ingresses
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - extensions
    resources:
      - ingresses/status
    verbs:
      - update
  - apiGroups:
      - traefik.containo.us
    resources:
      - middlewares
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - traefik.containo.us
    resources:
      - ingressroutes
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - traefik.containo.us
    resources:
      - ingressroutetcps
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - traefik.containo.us
    resources:
      - tlsoptions
    verbs:
      - get
      - list
      - watch

---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: traefik-ingress-controller

roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: traefik-ingress-controller
subjects:
  - kind: ServiceAccount
    name: traefik-ingress-controller
    namespace: default

#traefik-svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: traefik

spec:
  type: NodePort
  ports:
    - protocol: TCP
      name: web
      port: 8000
      nodePort: 20000
    - protocol: TCP
      name: admin
      port: 8080
      nodePort: 20001
    - protocol: TCP
      name: websecure
      port: 4443
      nodePort: 20002
  selector:
    app: traefik

#traefik-deploy.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  namespace: default
  name: traefik-ingress-controller

---
kind: Deployment
apiVersion: apps/v1
metadata:
  namespace: default
  name: traefik
  labels:
    app: traefik

spec:
  replicas: 2
  selector:
    matchLabels:
      app: traefik
  template:
    metadata:
      labels:
        app: traefik
    spec:
      serviceAccountName: traefik-ingress-controller
      containers:
        - name: traefik
          image: harbor.od.com/public/traefik:v2.0.2
          args:
            - --api.insecure
            - --accesslog
            - --entrypoints.web.Address=:8000
            - --entrypoints.websecure.Address=:4443
            - --providers.kubernetescrd
            - --certificatesresolvers.default.acme.tlschallenge
            - --certificatesresolvers.default.acme.email=foo@you.com
            - --certificatesresolvers.default.acme.storage=acme.json
            # Please note that this is the staging Let's Encrypt server.
            # Once you get things working, you should remove that whole line altogether.
            - --certificatesresolvers.default.acme.caserver=https://acme-staging-v02.api.letsencrypt.org/directory
          ports:
            - name: web
              containerPort: 8000
            - name: websecure
              containerPort: 4443
            - name: admin
              containerPort: 8080

#HDSS7-21
kubectl create -f http://k8s-yaml.od.com/traefik/traefik-crd.yaml 
kubectl create -f http://k8s-yaml.od.com/traefik/traefik-deploy.yaml 
kubectl create -f http://k8s-yaml.od.com/traefik/traefik-svc.yaml 


#ingress
#ingress.yaml
apiVersion: traefik.containo.us/v1alpha1
kind: IngressRoute
metadata:
  name: pro
  namespace: default
spec:
  entryPoints:
    - web
  routes:
  - match: Host(`traefik.od.com`) && PathPrefix(`/`)
    kind: Rule
    services:
    - name: test-server
      port: 8080
          
          
#vi /etc/nginx/conf.d/od.com.conf
upstream default_backend_traefik {
	server 10.4.7.21:20000 max_fails=3 fail_timeout=10s;
	server 10.4.7.22:20000 max_fails=3 fail_timeout=10s;
}
server {
	server_name *.od.com;
	location / {
		proxy_pass http://default_backend_traefik;
		proxy_set_header Host	$http_host;
		proxy_set_header x_forwarded_for $proxy_add_x_forwarded_for;
	}
}
```

### ingress

```shell
wget  http://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.18.0/deploy/mandatory.yaml
在
serviceAccountName: nginx-ingress-serviceaccount 下加一句
      hostNetwork: true
      
#ingress.yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: https-web
  annotations:
    kuberenetes.io/ingress.class: "nginx"
spec:
  rules:
  - host: traefik.od.com
    http:
      paths:
      - path: /
        backend:
          serviceName: nginx
          servicePort: 80

```

```yaml
vim dashboard-adminuser.yaml
---
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-app: kubernetes-dashboard
    name: kubernetes-dashboard-admin
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard-admin
  labels:
    k8s-app: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard-admin
  namespace: kube-system
```

