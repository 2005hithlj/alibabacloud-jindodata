# JindoSDK + COS 快速入门

JindoSDK 全面兼容 Hadoop FileSystem 接口，提供了更好的兼容性和易用性。
相对于 Hadoop 社区 COS 客户端实现，您仍然可以获得更好的性能和阿里云 E-MapReduce 产品技术团队更专业的支持。

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

### 3. 配置 JindoSDK COS 实现类及 Access Key

将 JindoSDK COS 实现类配置到 Hadoop 的 `core-site.xml` 中。

```xml
<configuration>
    <property>
        <name>fs.AbstractFileSystem.cos.impl</name>
        <value>com.aliyun.jindodata.cos.COS</value>
    </property>

    <property>
        <name>fs.cos.impl</name>
        <value>com.aliyun.jindodata.cos.JindoCosFileSystem</value>
    </property>
</configuration>

```

### 4. 配置 COS Access Key

将 Access Key 配置到 Hadoop 的 `core-site.xml` 中。

#### 配置 COS Access Key
将 COS bucket 对应 的`Access Key ID`、`Access Key Secret`、`Endpoint` 等预先配置在 Hadoop 的`core-site.xml`中。
```xml
<configuration>
    <property>
        <name>fs.cos.accessKeyId</name>
        <value>xxx</value>
    </property>

    <property>
        <name>fs.cos.accessKeySecret</name>
        <value>xxx</value>
    </property>

    <property>
        <name>fs.cos.endpoint</name>
        <value>xxx</value>
    </property>
</configuration>
```

#### 按照 bucket 配置 Access Key

JindoSDK 还支持不同的 COS bucket 配置不同的 Access Key。

```xml
<configuration>
    <property>
        <name>fs.cos.bucket.XXX.accessKeyId</name>
        <value>COS bucket 的 AccessKey Id</value>
    </property>
    <property>
        <name>fs.cos.bucket.XXX.accessKeySecret</name>
        <value>COS bucket 的A ccessKey Secret</value>
    </property>
</configuration>
```
说明 XXX 为 COS bucket 名称。

