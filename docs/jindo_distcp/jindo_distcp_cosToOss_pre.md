### 使用前须知
* 请参考 [Jindo DistCp 介绍](jindo_distcp_overview.md) 文章内容进行环境适配和工具包下载
* 如您在使用过程中遇到问题可参考 [Jindo DistCp 问题排查指南](jindo_distcp_QA_pre.md) 进行解决，也可 [新建 ISSUE](https://github.com/aliyun/alibabacloud-jindo-sdk/issues/new) 向我们反馈

### 1、下载 COS 依赖包
下载[依赖包](https://github.com/tencentyun/hadoop-cos/releases), hadoop-cos-{hadoop.version}-x.x.x.jar 和 cos_api-bundle-5.x.x.jar。
如：cos_api-bundle-5.6.35.jar, hadoop-cos-2.8.5-5.9.2.jar

### 2、配置 COS 相关实现类和用户信息
修改Hadoop的core-site.xml，增加 COS 相关用户和实现类信息。
```
<configuration>
    <property>
        <name>fs.cosn.impl</name>
        <value>org.apache.hadoop.fs.CosFileSystem</value>
    </property>
    <property>
        <name>fs.AbstractFileSystem.cosn.impl</name>
        <value>org.apache.hadoop.fs.CosN</value>
    </property>

    <property>
        <name>fs.cosn.userinfo.secretId</name>
        <value>xxx</value>
    </property>
    <property>
        <name>fs.cosn.userinfo.secretKey</name>
        <value>xxx</value>
    </property>
    <property>
        <name>fs.cosn.bucket.endpoint_suffix</name>
        <value>cos.ap-shanghai.myqcloud.com</value>
    </property>
</configuration>
```

### 3、拷贝数据到 OSS 上
您可以使用如下命令将 COS 上的目录拷贝到 OSS 上，您可使用如下命令
```
hadoop jar jindo-distcp-3.4.0.jar -libjars cos_api-bundle-5.6.35.jar,hadoop-cos-2.8.5-5.9.2.jar --src cosn://srcbucket/ --dest oss://destBucket/ --ossKey yourkey --ossSecret yoursecret --ossEndPoint oss-cn-xxx.aliyuncs.com --parallelism 10
```
* --src：cos 的源路径
* --dest：oss 的目标路径
* --ossKey：oss 的 AccessKey
* --ossSecret：oss 的 AccessSecret
* --ossEndPoint：oss 的 endpoint 信息，可以公网或者内网的 endpoint
* --parallelism：任务并发大小，根据集群资源可调整

### 4、增量拷贝文件
如果 Distcp 任务因为各种原因中间失败了，而此时您想进行断点续传，只Copy剩下未Copy成功的文件。或者源端文件新增了部分文件，此时需要您在进行上一次 Distcp 任务完成后进行如下操作：
##### 使用 --diff 命令，获得增量的文件列表
```
hadoop jar jindo-distcp-3.4.0.jar -libjars cos_api-bundle-5.6.35.jar,hadoop-cos-2.8.5-5.9.2.jar --src cosn://srcbucket/ --dest oss://destBucket/ --ossKey yourkey --ossSecret yoursecret --ossEndPoint oss-cn-xxx.aliyuncs.com --diff
```
如果所有文件都传输完成，则会提示如下信息。
```
INFO distcp.JindoDistCp: distcp has been done completely.
```
##### 增量的文件列表会被写入到本地的 manifest 文件里，默认生成在当前提交任务的路径下，您可以使用如下命令进行剩余文件的Copy
```
hadoop jar jindo-distcp-3.4.0.jar -libjars cos_api-bundle-5.6.35.jar,hadoop-cos-2.8.5-5.9.2.jar --src cosn://srcbucket/ --dest oss://destBucket/ --ossKey yourkey --ossSecret yoursecret --ossEndPoint oss-cn-xxx.aliyuncs.com --previousManifest=file:///opt/manifest-2020-04-17.gz --copyFromManifest --parallelism 20
```
* --copyFromManifest：表示从文件本地文件列表中读取文件
* --previousManifest：需要拷贝的文件列表，通过 --diff 生成

### 5、文件冷备份
如您想对写入到 OSS 上的文件进行冷备，如转化成归档（archive）和低频（ia）文件，可利用 Jindo DistCp 直接进行该流程

##### 写入归档文件
```
hadoop jar jindo-distcp-3.4.0.jar -libjars cos_api-bundle-5.6.35.jar,hadoop-cos-2.8.5-5.9.2.jar --src cosn://srcbucket/ --dest oss://destBucket/ --ossKey yourkey --ossSecret yoursecret --ossEndPoint oss-cn-xxx.aliyuncs.com --policy archive --parallelism 10
```
* --policy archive：表示写入到 OSS 文件以归档文件形式存放
##### 写入低频文件
```
hadoop jar jindo-distcp-3.4.0.jar -libjars cos_api-bundle-5.6.35.jar,hadoop-cos-2.8.5-5.9.2.jar --src cosn://srcbucket/ --dest oss://destBucket/ --ossKey yourkey --ossSecret yoursecret --ossEndPoint oss-cn-xxx.aliyuncs.com --policy ia --parallelism 10
```
* --policy ia：表示写入到 OSS 文件以低频文件形式存放

### 6、YARN 队列及带宽选择
如您需要对 DistCp 作业使用的 YARN 队列和带宽进行限定，可用如下命令
```
hadoop jar jindo-distcp-3.4.0.jar -libjars cos_api-bundle-5.6.35.jar,hadoop-cos-2.8.5-5.9.2.jar --src cosn://srcbucket/ --dest oss://destBucket/ --ossKey yourkey --ossSecret yoursecret --ossEndPoint oss-cn-xxx.aliyuncs.com --queue yarnQueue --bandwidth 100 --parallelism 10
```
* --queue：指定 YARN 队列的名称
* --bandwidth：指定限流带宽的大小，单位 MB

### 7、OSS 密钥固定存储
通常您需要将 OSS 的 AccessKey/AccessSecret/EndPoint 信息写在参数里，但是Jindo DistCp可以将其预先写在 Hadoop 的core-site.xml文件里，以避免使用时多次填写的问题。
如果您需要保存 OSS 的相关信息，您需要将以下信息保存在core-site.xml中，可省去在命令行里填写 ossKey/ossSecret/ossEndPoint 等信息。
```
<configuration>
    <property>
        <name>fs.jfs.cache.oss-accessKeyId</name>
        <value>xxx</value>
    </property>

    <property>
        <name>fs.jfs.cache.oss-accessKeySecret</name>
        <value>xxx</value>
    </property>

    <property>
        <name>fs.jfs.cache.oss-endpoint</name>
        <value>oss-cn-xxx.aliyuncs.com</value>
    </property>
</configuration>
```
JindoFS还支持更多的OSS AccessKey的配置方式，详情参考[JindoFS SDK OSS AccessKey 配置](./jindofs_sdk_credential_provider.md)。<br />

### 8、其他功能
如您需要其他使用其他功能，请参考
* [Jindo DistCp 进行数据迁移的使用说明](jindo_distcp_how_to.md)
* [Jindo DistCp 场景化使用指南](jindo_distcp_scenario_guidance.md)
