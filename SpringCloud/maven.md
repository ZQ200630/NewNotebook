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

