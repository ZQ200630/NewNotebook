# Nexus Maven私服
### 配置到pom.xml 上传依赖
```xml
<distributionManagement>
    <snapshotRepository>
        <id>nexus</id>
        <name>Nexus Snapshot</name>
        <url>http://localhost:8081/repository/maven-snapshots/</url>
    </snapshotRepository>

    <repository>
        <id>nexus</id>
        <name>Nexus Snapshot</name>
        <url>http://localhost/repository/maven-releases/</url>
    </repository>
</distributionManagement>
```

### 配置仓库位置IP
```xml
<repositories>
    <repository>
        <id>maven-central</id>
        <name>maven-central</name>
        <url>http://localhost:8081/repository/maven-public/</url>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
        <releases>
            <enabled>true</enabled>
        </releases>
    </repository>
</repositories>
```

### 配置到maven安装目录conf/setting.xml
```xml
<server>
    <id>nexus</id>
    <username>admin</username>
    <password>ZHANGqian200630</password>
</server>
```

### Docker-compose

```yaml
version: '3.1'
services:
    nexus:
        restart: always
        image: sonatype/nexus3
        container_name: nexus
        ports:
            - 8081:8081
        volumes:
            - ./data:/nexus-data

```

### Volume

- 在文件夹中新建data

# Gitlab 代码服务器

### 启动方式

- 新建/config
- 新建/data
- 新建/logs

- 先/opt启动一次，不要停再/opt/data启动

### Docker-compose

```yaml
version: '3'
services:
    web:
        image: 'twang2218/gitlab-ce-zh'
        restart: always
        hostname: 'localhost'
        environment:
            TZ: 'Asia/Shanghai'
            GITLAB_OMNIBUS_CONFIG: |
                external_url 'http://localhost'
                gitlab_rails['gitlab_shell_ssh_port'] = 2222
                unicorn['port'] = 8888
                nginx['listen_port'] = 80
        ports:
            - '80:80'
            - '443:443'
            - '2222:22'
        volumes:
            - ./config:/etc/gitlab
            - ./data:/var/opt/gitlab
            - ./logs:/var/log/gitlab
```

