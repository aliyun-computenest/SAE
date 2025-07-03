# Dify 社区版计算巢一键部署


>**免责声明：**本服务由第三方提供，我们尽力确保其安全性、准确性和可靠性，但无法保证其完全免于故障、中断、错误或攻击。因此，本公司在此声明：对于本服务的内容、准确性、完整性、可靠性、适用性以及及时性不作任何陈述、保证或承诺，不对您使用本服务所产生的任何直接或间接的损失或损害承担任何责任；对于您通过本服务访问的第三方网站、应用程序、产品和服务，不对其内容、准确性、完整性、可靠性、适用性以及及时性承担任何责任，您应自行承担使用后果产生的风险和责任；对于因您使用本服务而产生的任何损失、损害，包括但不限于直接损失、间接损失、利润损失、商誉损失、数据损失或其他经济损失，不承担任何责任，即使本公司事先已被告知可能存在此类损失或损害的可能性；我们保留不时修改本声明的权利，因此请您在使用本服务前定期检查本声明。如果您对本声明或本服务存在任何问题或疑问，请联系我们。

## 概述

Dify.AI 是一款 LLMOps 平台，帮助开发者更简单、更快速地构建 AI 应用。它的核心理念是通过可声明式的 YAML 文件定义 AI 应用的各个方面，包括 Prompt、上下文和插件等。Dify 提供了可视化的 Prompt 编排、运营、数据集管理等功能。这些功能使得开发者能够在数天内完成 AI 应用的开发，或将 LLM 快速集成到现有应用中，并进行持续运营和改进，创造一个真正有价值的 AI 应用。
本方案提供了部署 Dify 的最佳实践，基于阿里云 Serverless 应用引擎（SAE）部署的 Dify 支持高可用、弹性伸缩、可观测、免运维等能力，集成了阿里云文件存储、数据库服务，可以实现 Dify 稳定、高性能部署，推荐用于生产环境。

## 前提条件

部署 Dify 社区版服务实例，需要对部分阿里云资源进行访问和创建操作。因此您的账号需要包含如下资源的权限。
**说明**：当您的账号是RAM账号时，才需要添加此权限。

| 权限策略名称                          | 备注                               |
|---------------------------------|----------------------------------|
| AliyunSAEFullAccess             | 管理 Serverless 应用引擎（SAE）的权限       |
| AliyunVPCFullAccess             | 管理专有网络（VPC）的权限                   |
| AliyunSLBFullAccess             | 管理负载均衡服务（SLB）的权限                 |
| AliyunROSFullAccess             | 管理资源编排服务（ROS）的权限                 |
| AliyunKvstoreFullAccess         | 管理云数据库 Tair（兼容 Redis）的权限         |
| AliyunRDSFullAccess             | 管理云数据库服务（RDS）的权限                 |
| AliyunGPDBFullAccess            | 管理 AnalyticDB for PostgreSQL 的权限 |
| AliyunNASFullAccess            | 管理文件存储服务（NAS）的权限                 |
| AliyunComputeNestUserFullAccess | 管理计算巢服务（ComputeNest）的用户侧权限       |


## 计费说明

Dify 社区版在计算巢部署的费用主要涉及：
- SAE CPU、内存规格与副本数
- 云数据库的规格
- NAS 中写入文件数据产生的实际存储容量
- 可观测写入数据量（可选）

## 部署架构
![image.png](https://img.alicdn.com/imgextra/i1/O1CN01skRMOT1LgjRRQhXdw_!!6000000001329-2-tps-1908-1430.png)

本方案的技术架构包括以下基础设施和云服务：
- 1个专有网络 VPC：为 SAE 应用、云数据库等云资源构建云上私有网络。
- 2个交换机：将 SAE 应用、云数据库等资源连接在同一网络上，实现它们之间的通信，并提供基本的网络分段和隔离功能。
- 1个安全组：用于限制专有网络 VPC 下的网络出入规则。
- 1个负载均衡 CLB：为 Dify 提供负载均衡服务，将流量分发到不同的后端服务器来扩展应用系统的服务吞吐能力。
- 1个云数据库 Tair（兼容 Redis）：为 Dify 提供缓存服务。
- 1个云原生数据仓库 AnalyticDB PostgreSQL版：为 Dify 提供向量检索服务。
- 1个云数据库 RDS PostgreSQL版：为 Dify 提供关系型数据库服务。
- 1个文件存储 NAS：用于 Dify 应用文件存储。
- 1个 NAT 网关：用于 Dify 应用访问公网服务。
- 1个弹性公网IP：用于 Dify 应用访问公网服务。
- Serverless应用引擎SAE：用于提供部署 Dify 应用的基础资源。

## 部署流程
1. 选择模板，模板可选择"基础版"或"可观测版"，可观测版集成了 api/sandbox/plugin-daemon 三个组件的可观测功能，通过探针自动埋点，提供性能分析、token分析、调用链视图等功能。
   ![image.png](https://img.alicdn.com/imgextra/i2/O1CN01gAyQnx1yQ20xtCiVb_!!6000000006572-2-tps-1926-204.png)
   配置 SAE 命名空间、实例规格与副本数。
   ![image.png](https://img.alicdn.com/imgextra/i3/O1CN014hDAaj1fei0WhD8jZ_!!6000000004032-2-tps-1920-744.png)
   配置Postgres数据库
   ![image.png](https://img.alicdn.com/imgextra/i2/O1CN01TlVpru1saD7g7C1wP_!!6000000005782-2-tps-2766-1628.png)
   配置Reids数据库
   ![image.png](https://img.alicdn.com/imgextra/i2/O1CN01zbF45n1aBmv0PAV69_!!6000000003292-2-tps-2688-398.png)
   配置向量数据库
   ![image.png](https://img.alicdn.com/imgextra/i2/O1CN01UR5fHl1V0Gsg16wsD_!!6000000002590-2-tps-2776-684.png)
   配置网络参数，可以选择新建 VPC, 也可以选择已有 VPC，推荐双可用区部署，提高服务可用性。
   ![image.png](https://img.alicdn.com/imgextra/i2/O1CN01XCIhHM1HV2s9L4ISM_!!6000000000762-2-tps-2722-1080.png)
   若选择已有 VPC，且该 VPC 未配置 NAT 网关，需要新建 NAT 网关以支持实例公网访问。
   ![image.png](https://img.alicdn.com/imgextra/i4/O1CN01cT9FBW1V83c6bZyhR_!!6000000002607-2-tps-2770-1144.png)
2. 参数填写完成后可以看到对应询价明细（**注意：如果选择可观测版，会额外产生 ARMS 收费，这部分费用取决于使用过程中产生的数据量，不会计入询价**）。确认参数后点击**下一步：确认订单**。 确认订单完成后同意服务协议并点击**立即创建**进入部署阶段。
   ![image.png](https://img.alicdn.com/imgextra/i3/O1CN01vR1uXc1Ex2EHT2H6J_!!6000000000417-2-tps-2992-1518.png)
   ![image.png](https://img.alicdn.com/imgextra/i3/O1CN01jikKcW1xxB3vGVKDA_!!6000000006509-2-tps-3354-1808.png)
3. 等待部署完成后就可以开始使用服务，进入服务实例详情获取 Dify 访问入口。
   ![image.png](https://img.alicdn.com/imgextra/i4/O1CN01vV8cHG1srcAmye0pw_!!6000000005820-2-tps-2992-1518.png)
4. 注册账号。
   ![image.png](https://img.alicdn.com/imgextra/i1/O1CN01Wg16xF1kOkHXAWUbl_!!6000000004674-2-tps-2992-1786.png)
5. 登录使用。
   ![image.png](https://img.alicdn.com/imgextra/i2/O1CN01LoGIYV1LwIuMKHxvA_!!6000000001363-2-tps-2992-1784.png)

## 使用说明
### SAE 应用管理
进入 SAE 控制台，选择应用所在命名空间，进入应用详情，即可对应用配置及生命周期进行管理。
![image.png](https://img.alicdn.com/imgextra/i1/O1CN01NHmWF71FGjGDuu7Ym_!!6000000000460-2-tps-2318-1358.png)

### 配置项及保密字典管理
如果希望更改 Dify 应用的配置，可以参考以下文档。
[管理和使用配置项（K8s ConfigMap）](https://help.aliyun.com/zh/sae/manage-and-use-configuration-items-k8s-configmap?spm=a2c4g.11186623.help-menu-118957.d_2_4_1.25d72c20oW6r2w&scm=20140722.H_2773560._.OR_help-T_cn~zh-V_1)
[管理和使用保密字典（K8s Secret)](https://help.aliyun.com/zh/sae/managing-and-using-a-confidential-dictionary-k8s-secret?spm=a2c4g.11186623.help-menu-118957.d_2_4_2.345c2fa080wB5p)

### 查看应用监控
进入 SAE 应用详情。选择应用监控页签，即可查看应用监控数据。
![image.png](https://img.alicdn.com/imgextra/i3/O1CN01vQPPg21jGDHuKjo06_!!6000000004520-2-tps-2992-1520.png)
也可以通过 ARMS 控制台查看应用监控数据。
![image.png](https://img.alicdn.com/imgextra/i1/O1CN01IcD4l31tnK4pxTZQZ_!!6000000005946-2-tps-2992-1630.png)


### 配置 Dify 服务的弹性伸缩
如果希望实现弹性扩缩容， 可以参考以下文档。
[配置弹性伸缩策略](https://help.aliyun.com/zh/sae/serverless-app-engine-upgrade/user-guide/configure-auto-scaling?scm=20140722.S_help%40%40%E6%96%87%E6%A1%A3%40%402773678._.ID_help%40%40%E6%96%87%E6%A1%A3%40%402773678-RL_%E5%BC%B9%E6%80%A7-LOC_doc%7EUND%7Eab-OR_ser-PAR1_2102029b17515227611745768d21ce-V_4-RE_new5-P0_1-P1_0&spm=a2c4g.11186623.help-search.i27)。
