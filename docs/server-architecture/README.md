# 服务端架构

[返回项目总览](../../README.md)

本章从系统拆分、依赖方向、通信执行、数据一致性和云原生韧性五个维度整理服务端架构。服务端架构应从简单部署开始，只有在业务边界、团队自治、容量或可用性指标提供证据时再增加分布式复杂度。

## 目录

- [系统拆分与部署边界](#系统拆分与部署边界)
- [依赖方向与业务组织](#依赖方向与业务组织)
- [通信与执行模型](#通信与执行模型)
- [数据、状态与一致性](#数据状态与一致性)
- [云原生、伸缩与韧性](#云原生伸缩与韧性)
- [常见组合](#常见组合)

## 系统拆分与部署边界

| 架构 | 核心思想 | 适合 | 主要代价 |
|------|----------|------|----------|
| 单体架构 | 所有业务在一个部署单元中运行 | 小团队、MVP、边界不明确 | 独立伸缩和发布困难 |
| 分层单体 | 单体内部按展示、业务、数据访问分层 | 管理系统和企业应用 | 一次变更可能穿透多层 |
| 模块化单体 | 单一部署，按业务模块建立强边界 | 中型系统、简单运维 | 需要依赖规则保证边界 |
| 垂直切片架构 | 按用例或功能纵向组织逻辑 | 高频迭代、功能自治 | 共享逻辑和一致性治理 |
| 面向服务架构 | 企业能力由粗粒度服务和契约暴露 | 企业集成、遗留系统整合 | ESB 和中心治理可能变重 |
| 微服务 | 按业务能力拆成独立部署和独立数据的服务 | 复杂域、多团队、独立伸缩 | 分布式事务、可观测性和运维成本 |
| Self-Contained System | 每个业务切片包含 UI、逻辑和数据 | 大型 Web 产品的团队自治 | 跨系统体验和共享数据协调 |
| 微内核 / 插件式服务 | 核心流程稳定，业务变体通过插件扩展 | 规则平台、支付渠道、SaaS 定制 | 插件安全和兼容治理 |
| Serverless / FaaS | 事件触发短生命周期函数，平台管理基础设施 | 波动流量、事件处理 | 冷启动、平台约束和分布式调试 |
| Cell-Based Architecture | 将完整服务栈复制为多个隔离 Cell | 大规模 SaaS、限制故障半径 | Cell 路由、容量和跨 Cell 操作 |
| Multi-Tenant | 多租户共享应用并按层级隔离资源 | SaaS | 数据隔离、噪声邻居和定制化 |
| 单租户 / Silo | 每租户独立部署或数据栈 | 高合规、高隔离客户 | 成本和版本运维压力 |

## 依赖方向与业务组织

| 架构 | 核心思想 | 适合 | 主要代价 |
|------|----------|------|----------|
| N-Tier / 三层 | 展示、业务和数据按逻辑层或物理层分离 | 传统企业应用、迁移上云 | 层间耦合和无效转发 |
| MVC | Controller 协调请求，Model 承载数据/业务，View 展示 | 服务端页面、API 控制层 | Controller 和 Model 容易膨胀 |
| Hexagonal | 领域通过端口与外部世界交互，适配器实现技术细节 | 独立测试和替换基础设施 | 接口和映射代码增加 |
| Onion Architecture | 领域位于中心，依赖由外向内 | 复杂业务和长期演进系统 | 简单 CRUD 可能过度设计 |
| Clean Architecture | Entity、Use Case 在内层，框架和 IO 在外层 | 多入口、多存储、强测试系统 | 分层、DTO 和转换成本 |
| Domain-Driven Design | 以统一语言、聚合和限界上下文建模业务 | 复杂且持续演化的核心域 | 建模投入高 |
| CQRS | 命令和查询使用不同模型或路径 | 读写差异大、复杂业务规则 | 最终一致性和模型同步 |
| CRUD / Transaction Script | 每个请求直接执行读取或事务脚本 | 简单业务、后台管理 | 复杂规则下重复和耦合增长 |
| Pipeline / Pipes and Filters | 请求或数据依次经过独立处理阶段 | 网关、ETL、媒体处理 | 错误传播和上下文管理 |

## 通信与执行模型

| 架构 | 核心思想 | 适合 | 主要代价 |
|------|----------|------|----------|
| Client-Server | 客户端请求集中式服务提供能力 | 大多数网络应用 | 服务端容量和可用性 |
| REST | 以资源和统一 HTTP 语义组织接口 | 公共 API、Web/移动后端 | 聚合和实时推送不直接 |
| RPC | 像调用方法一样调用远程服务 | 内部高性能、强类型服务通信 | 容易忽视网络不可靠性 |
| GraphQL | 客户端声明字段，由 Schema 统一查询 | 多终端、数据聚合 API | N+1、缓存、权限和成本控制 |
| API Gateway | 单入口完成路由、认证、限流和协议转换 | 微服务对外入口 | 可能成为瓶颈和集中耦合点 |
| Backend for Frontend | 每类客户端拥有专用后端适配层 | 多端需求差异大 | 服务数量和逻辑重复 |
| Web-Queue-Worker | Web 接收请求，队列把长任务交给 Worker | 图片、报表、邮件、批任务 | 异步状态、幂等和失败补偿 |
| 事件驱动架构 | Producer 发布事件，Consumer 异步响应 | 高吞吐、松耦合、实时集成 | 顺序、重复、追踪和最终一致性 |
| Publish-Subscribe | 一个事件广播给多个独立订阅者 | 通知、集成、领域事件 | 投递语义和订阅治理 |
| Competing Consumers | 多消费者竞争处理队列任务 | 后台任务和水平伸缩 | 幂等、超时和毒消息 |
| Actor Model | Actor 封装状态，通过异步消息通信 | 高并发、有状态实体 | 消息顺序和调试 |
| Reactive Architecture | 以消息驱动、弹性、韧性和响应性组织系统 | 实时、高并发系统 | 异步心智和背压治理 |
| Peer-to-Peer | 节点既是客户端也是服务端 | 内容分发、区块链、协作网络 | 发现、信任和一致性 |
| Choreography | 服务监听事件并自主推进流程 | 松耦合、简单跨服务流程 | 全局流程不可见 |
| Orchestration | 中央协调器命令各服务执行流程 | 复杂工作流、统一状态管理 | 协调器耦合和高可用要求 |

## 数据、状态与一致性

| 架构 / 模式 | 核心思想 | 适合 | 主要代价 |
|-------------|----------|------|----------|
| Shared Database | 多模块或服务共享数据库 | 强事务、早期系统 | Schema 耦合，难独立发布 |
| Database per Service | 每个服务拥有自己的数据边界 | 微服务自治 | 跨服务查询和事务复杂 |
| Event Sourcing | 追加保存状态变化事件，通过重放恢复状态 | 审计、时态查询、复杂领域 | 事件演进和重放成本 |
| Materialized View | 预计算面向查询的只读视图 | CQRS、聚合查询、报表 | 刷新延迟和重建机制 |
| Saga | 用本地事务和补偿动作完成跨服务业务事务 | 微服务长事务 | 补偿不总是可逆 |
| Transactional Outbox | 业务数据和待发布事件在同一事务写入 | 数据库与消息一致性 | 转发、清理和重复投递 |
| Inbox / Idempotent Consumer | 记录已处理消息或用业务键去重 | 至少一次投递系统 | 去重存储和保留周期 |
| Two-Phase Commit | 多参与者准备后统一提交 | 少数强一致受控环境 | 阻塞、可用性和伸缩性差 |
| Sharding / Partitioning | 按键将数据和负载拆到多个节点 | 海量数据和吞吐 | 热点、重分片和跨分片查询 |
| Replication | 数据复制到多个节点 | 读扩展和容灾 | 延迟、冲突和一致性选择 |
| Leader-Follower | Leader 写入，Follower 复制、读取或接管 | 数据库和日志系统 | Leader 瓶颈和复制延迟 |
| Leaderless / Quorum | 多副本可读写，以仲裁获得一致性 | 高可用分布式存储 | 冲突合并和参数选择 |
| Shared-Nothing | 每个节点独立拥有计算、内存和磁盘 | 水平伸缩集群 | 重分布和跨节点协调 |
| Lambda Architecture | 批处理层和实时层并行，查询层合并结果 | 历史准确性 + 低延迟 | 两套处理逻辑 |
| Kappa Architecture | 所有数据作为流处理，历史通过重放计算 | 流式数据平台 | 长期日志和重放资源 |

## 云原生、伸缩与韧性

| 架构 / 模式 | 核心思想 | 解决的问题 | 主要注意点 |
|-------------|----------|------------|------------|
| Stateless Service | 请求不依赖本机持久会话 | 水平伸缩、故障切换 | 状态需外置 |
| Stateful Service | 实例持有分区状态并管理身份和存储 | 数据库、Actor、流处理 | 调度、复制和恢复 |
| Sidecar | 辅助进程与主应用同生命周期部署 | 日志、代理、配置、遥测 | 每实例资源开销 |
| Ambassador | 旁路代理代表应用访问外部服务 | 连接、重试、协议转换 | 代理故障和额外跳数 |
| Service Mesh | 数据面代理统一通信，控制面下发策略 | mTLS、流量治理、可观测性 | 资源、延迟和平台复杂度 |
| Circuit Breaker | 连续失败时快速拒绝，稍后探测恢复 | 防止级联故障 | 阈值和恢复窗口 |
| Retry with Backoff | 对暂时性故障按退避策略重试 | 网络抖动、限流 | 幂等和重试风暴 |
| Timeout / Deadline | 给调用链设置明确时间预算 | 无限等待和资源泄漏 | 预算沿调用链传播 |
| Bulkhead | 隔离线程池、连接池、实例或租户资源 | 限制故障半径 | 容量切分降低利用率 |
| Rate Limiting | 限制主体的请求或资源消耗 | 过载保护、公平使用 | 突发策略和分布式计数 |
| Load Shedding | 过载时主动拒绝低优先级工作 | 保住核心能力 | 优先级和用户体验 |
| Cache-Aside | 缓存未命中时查源并回填 | 降低延迟和数据库压力 | 失效、击穿、雪崩和一致性 |
| Autoscaling | 按负载、队列或业务指标增减实例 | 弹性容量和成本 | 指标滞后、冷启动和抖动 |
| Active-Passive | 备用区域平时少承载或不承载流量 | 灾备、降低成本 | 切换时间和恢复点 |
| Active-Active | 多区域同时服务并互相容灾 | 全球低延迟、高可用 | 写冲突、路由和成本 |
| Deployment Stamp | 复制完整部署单元承载一批流量 | 规模化、隔离和版本发布 | 分配和跨 Stamp 运维 |
| Strangler Fig | 在旧系统外围逐步用新实现替换功能 | 遗留系统渐进迁移 | 路由、双写和长期过渡 |
| Anti-Corruption Layer | 在新旧模型间翻译 | 遗留集成、并购系统 | 映射层复杂或成瓶颈 |

## 常见组合

| 场景 | 推荐起点 | 随复杂度增加可引入 |
|------|----------|--------------------|
| 小型后台 / MVP | 模块化单体 + 垂直切片 + CRUD | Cache-Aside、Web-Queue-Worker |
| 复杂企业核心系统 | 模块化单体 + DDD + Hexagonal | CQRS、Outbox、事件驱动 |
| 大型互联网后端 | 微服务 + API Gateway/BFF + Database per Service | Saga、Service Mesh、Cell |
| 实时数据平台 | 事件驱动 + Pub/Sub + Kappa | Materialized View、分片 |
| 遗留系统现代化 | Strangler Fig + Anti-Corruption Layer | Outbox、逐域微服务化 |

## 参考资料

- [Azure Architecture Styles](https://learn.microsoft.com/azure/architecture/guide/architecture-styles/)
- [Azure Cloud Design Patterns](https://learn.microsoft.com/azure/architecture/patterns/)
- [AWS Hexagonal Architecture](https://docs.aws.amazon.com/prescriptive-guidance/latest/hexagonal-architectures/overview.html)
- [Common Web Application Architectures on .NET](https://learn.microsoft.com/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures)
- *Designing Data-Intensive Applications* — Martin Kleppmann
- *Software Architecture: The Hard Parts* — Neal Ford、Mark Richards、Pramod Sadalage、Zhamak Dehghani
