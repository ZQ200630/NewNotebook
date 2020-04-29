### IOS

```shell
> #用户模式(啥都干不了)
# #特权模式(可以查询与测试)
enable # 用户->特权
e? #查看有哪些命令
Router#conf #全局配置模式(啥都可以干)
configure terminal # 特权->全局配置 
end #回退特权模式
exit #返回上一级权限
ethernet #以太网口
fastethernet #快速以太网口
serial #串行口
interface f0/0 #进入接口配置
ip address ip netmask #配置ip
no shutdown # 打开接口!!!
show running-config #查看配置
show ip interface brief #查看ip配置
hostname ? #返回大写是参数，小写是命令
enable password 123 #添加密码
no enable password #删除密码
enable secret 123 #加密添加密码

no ip domain lookup #放敲错命令，关闭域名解析
line console 0 #进入控制台
logging synchronous #显示信息同步
no exec-timeout #关闭会话超时，防止一段时间不操作自动退出

```

### 常见协议

```shell
# telnet 不安全
# ftp 传输文件
# tftp 传输文件， ftp可以看到服务端目录，tftp看不到
# SNMP 网络管理协议
# DNS Domain Namespace 域名解析
A record: name -> ip
PTR record: ip -> name
# NTP 同步时间
# DHCP/BootP/APIPA

```

### LINUX路由

```shell
route -n #显示路由
route add -net 172.7.21.0/24 gw 10.4.7.21 dev eth0
route del-net 172.7.21.0/24 gw 10.4.7.21
```

