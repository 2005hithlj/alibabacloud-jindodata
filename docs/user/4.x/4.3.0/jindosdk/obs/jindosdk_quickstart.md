# JindoSDK + 阿里云 OBS 快速入门

JindoSDK 全面兼容 Hadoop FileSystem 接口，提供了更好的兼容性和易用性。
相对于 Hadoop 社区 OBS 客户端实现，您仍然可以获得更好的性能和阿里云 E-MapReduce 产品技术团队更专业的支持。

## 步骤

### 1. 下载 JindoSDK 包
下载最新的 tar.gz 包 jindosdk-x.x.x.tar.gz ([下载页面](/docs/user/4.x/jindodata_download.md))。

### 2. 配置环境变量

* 配置`JINDOSDK_HOME`

解压下载的安装包，以安装包内容解压在`/usr/lib/jindosdk-4.3.0`目录为例：
```bash
export JINDOSDK_HOME=/usr/lib/jindosdk-4.3.0
export PATH=$JINDOSDK_HOME/bin:$PATH
```
* 配置`HADOOP_CLASSPATH`

```bash
export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:${JINDOSDK_HOME}/lib/*
```

### 3. 配置 JindoSDK OBS 实现类

将 JindoSDK OBS 实现类配置到 Hadoop 的 `core-site.xml` 中。

```xml
<configuration>
    <property>
        <name>fs.AbstractFileSystem.obs.impl</name>
        <value>com.aliyun.jindodata.obs.OBS</value>
    </property>

    <property>
        <name>fs.obs.impl</name>
        <value>com.aliyun.jindodata.obs.JindoObsFileSystem</value>
    </property>
</configuration>
```

### 4. 配置 OBS Access Key

将 Access Key 配置到 Hadoop 的 `core-site.xml` 中。

#### 配置 OBS Access Key

将 OBS bucket 对应 的`Access Key ID`、`Access Key Secret`、`Endpoint` 等预先配置在 Hadoop 的`core-site.xml`中。
```xml
<configuration>
    <property>
        <name>fs.obs.accessKeyId</name>
        <value>xxx</value>
    </property>

    <property>
        <name>fs.obs.accessKeySecret</name>
        <value>xxx</value>
    </property>

    <property>
        <name>fs.obs.endpoint</name>
        <value>xxx</value>
    </property>
</configuration>
```

#### 按照 bucket 配置 Access Key

JindoSDK 还支持不同的 OBS bucket 配置不同的 Access Key。

```xml
<configuration>
    <property>
        <name>fs.obs.bucket.XXX.accessKeyId</name>
        <value>OBS bucket 的 AccessKey Id</value>
    </property>
    <property>
        <name>fs.obs.bucket.XXX.accessKeySecret</name>
        <value>OBS bucket 的 AccessKey Secret</value>
    </property>
</configuration>
```
说明 XXX 为 OBS bucket 名称。