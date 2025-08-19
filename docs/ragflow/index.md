# RAGFlow 高可用版 Serverless 部署

## 概述
RAGFlow是一个基于深度文档理解的开源RAG（检索增强生成）引擎。当与LLM集成时，它能够提供真实的问答功能，并得到各种复杂格式数据的充分引用的支持。 详情请查看[RAGFlow官网](https://ragflow.io)。
本方案提供了快速部署 RAGFlow 高可用版的最佳实践，基于阿里云 Serverless 应用引擎（SAE）部署的 RAGFlow 支持高可用、自动弹性、免运维等能力，集成了阿里云对象存储、MySQL/Redis 数据库、Elasticsearch 服务，可以实现 RAGFlow 稳定、高性能部署，推荐用于生产环境。


## 计费说明
RAGFlow 高可用版的费用主要涉及：

- 选vCPU与内存规格
- OSS 实际使用量
- MySQL/Redis 数据库规格
- Elasticsearch 实例规格


## RAM账号所需权限
部署RAGFlow 测试版，需要对部分阿里云资源进行访问和创建操作。因此您的账号需要包含如下资源的权限。
  **说明**：当您的账号是RAM账号时，才需要添加此权限。

| 权限策略名称                          | 备注                                 |
|---------------------------------|------------------------------------|
| AliyunSAEFullAccess             | 管理 Serverless 应用引擎（SAE）的权限       |
| AliyunVPCFullAccess             | 管理专有网络（VPC）的权限                     |
| AliyunROSFullAccess             | 管理资源编排服务（ROS）的权限                   |
| AliyunComputeNestUserFullAccess | 管理计算巢服务（ComputeNest）的用户侧权限         |
| AliyunOSSFullAccess              | 管理对象存储（OSS）的权限                       |
| AliyunElasticsearchFullAccess   | 管理 Elasticsearch 的权限            |
| AliyunKvstoreFullAccess         | 管理云数据库 Tair（兼容 Redis）的权限   |
| AliyunRDSFullAccess             | 管理云数据库服务（RDS）的权限           |
| AliyunSLBFullAccess             | 管理负载均衡服务（SLB）的权限           |

## 部署流程

1. 进入部署页面，选择模板，按提示填写部署参数：
  ![image.png](https://img.alicdn.com/imgextra/i2/O1CN01GxFN5y22Jr9SpAFQm_!!6000000007100-2-tps-2992-1528.png)

2. 参数填写完成后可以看到对应询价明细，确认参数后点击**下一步：确认订单**。确认订单完成后同意服务协议并点击**立即创建**进入部署阶段。 
3. 等待部署完成后进入服务实例管理, 在控制台找到RAGFlow服务访问链接。
  ![image.png](https://img.alicdn.com/imgextra/i2/O1CN01yDxcAx1SqzdjZuqCU_!!6000000002299-2-tps-2992-1524.png)
4. 单击链接访问服务。
  ![image.png](https://img.alicdn.com/imgextra/i2/O1CN01qBGTSx1ztdF2olWlD_!!6000000006772-0-tps-2878-1550.jpg)

## 常见问题
1. 使用通义千问API 报错API调用频率限制：
  ![image.png](https://img.alicdn.com/imgextra/i1/O1CN01TvqC6o1q6K3hN2e1p_!!6000000005446-2-tps-2564-822.png)
  解决方案：添加[Ragflow官方交流群](https://github.com/infiniflow/ragflow/blob/main/README_zh.md)协商修改使用限制。