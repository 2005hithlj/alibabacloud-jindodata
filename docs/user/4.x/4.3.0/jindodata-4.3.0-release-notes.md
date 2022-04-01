# JindoData 4.3.0 版本说明

## 版本概要
JindoData 是阿里云开源大数据团队自研的数据湖存储加速套件，面向大数据和 AI 生态，为阿里云和业界主要数据湖存储系统提供全方位访问加速解决方案。
JindoData 4.3.0 版本统一JindoSDK 的定义和规范，提供C SDK 供第三方开发以及集成，进一步完善多云存储方案。 优化以及改进JindoFSx 存储加速系统，支持Kerberos + Ranger 安全方案。

## 主要功能

### JindoSDK 和工具支持
- JindoSDK 支持多云存储，包括S3、COS、OBS。
- JindoSDK 提供统一的C SDK 支持，供第三方集成。
- JindoSDK 支持第三方开发和动态加载集成新的数据源和加速器XEngine。
- JindoSDK 支持JindoFSx 加速器。
- JindoSDK 支持Ranger 方案。

### JindoFSx 存储加速系统
- JindoFSx 后端系统加速扩展策略支持多云存储，包括S3、COS、OBS。
- JindoFSx 优化以及改进数据缓存以及元数据缓存方案。
- JindoFSx 支持 Kerberos + Ranger 方案。
- JindoFSx 完善相关系统可观测性指标，用户可以通过观测性指标观测 JindoFSx 存储加速系统。

### JindoFS 服务能力
- JindoFS 支持 POSIX Lock、Fallocate 能力。
- JindoFS 支持老版本 JindoFS Block 模式集群升级。

### JindoFuse POSIX 支持
- JindoFuse 新增XAttr相关接口支持，包括setxattr、getxattr、listxattr、removexattr。
- JindoFuse 支持Lock/Fallocate 相关接口。
