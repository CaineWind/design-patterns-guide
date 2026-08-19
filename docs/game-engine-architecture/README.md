# 游戏引擎架构

[返回项目总览](../../README.md)

本章从游戏世界的对象组织、逐帧执行、渲染并发和多人网络四个维度整理游戏引擎架构。架构并非互斥选项，例如一个大型实时游戏可以同时采用 ECS、数据导向设计、Job System、固定时间步长和权威服务器。

## 目录

- [游戏对象与世界组织](#游戏对象与世界组织)
- [游戏循环、并发与渲染](#游戏循环并发与渲染)
- [多人游戏网络架构](#多人游戏网络架构)
- [主流引擎架构映射](#主流引擎架构映射)
- [常见组合](#常见组合)

## 游戏对象与世界组织

| 架构 | 核心思想 | 优点 | 主要代价 | 代表引擎 / 场景 |
|------|----------|------|----------|-----------------|
| 类继承层次 | 通过基类和子类表达游戏对象类型 | 直观、面向对象工具支持好 | 深继承易僵化，复用粒度大 | 早期游戏引擎、GameMaker Object |
| GameObject-Component | 空壳对象挂载多个行为或数据组件 | 组合优于继承，编辑器友好 | 组件依赖和生命周期可能隐蔽 | Unity、Cocos Creator |
| Actor-Component | 可放入世界的 Actor 聚合组件并承担生命周期或网络职责 | 世界对象语义明确 | Actor 容易变成“大对象” | Unreal Engine |
| Entity-Component-System | Entity 是 ID，Component 保存数据，System 批量处理行为 | 高组合性，适合批处理和并行 | 关系建模和调试更难 | Unity Entities、Bevy、Flecs、EnTT |
| 数据导向设计 | 按访问模式和缓存局部性组织连续数据 | CPU 缓存友好，吞吐高 | 对传统 OOP 心智和工具不友好 | Unity DOTS、定制 AAA 引擎 |
| 场景图 | 用父子层次组织空间变换、可见性和渲染对象 | 空间关系直观 | 大规模动态场景更新成本高 | Godot、Cocos Creator、Three.js |
| Node-Scene 组合 | 节点是最小能力，场景是可复用的节点树 | 编辑、实例化和复用统一 | 跨树通信需额外约束 | Godot |
| Prefab / 原型实例 | 用模板定义对象结构，实例保存覆盖项 | 内容批量复用 | 嵌套覆盖和版本迁移复杂 | Unity Prefab、Unreal Blueprint、Godot PackedScene |
| 数据驱动架构 | 玩法、数值和关卡由资源或配置描述 | 内容迭代快，支持热更新 | Schema、校验和版本兼容要求高 | ScriptableObject、Data Asset、配置表 |
| 原生核心 + 脚本层 | 原生语言负责性能核心，脚本负责玩法 | 平衡性能和开发效率 | 跨语言调试、GC 和绑定成本 | Unreal、Godot、Cocos |
| 微内核 / 插件架构 | 最小核心通过插件或模块扩展能力 | 可裁剪、可替换 | 插件协议和版本治理复杂 | O3DE Gems、Unreal Plugins、Unity Packages |
| 子系统 / 服务架构 | 渲染、物理、音频等作为长生命周期服务 | 职责清晰，便于替换后端 | 容易形成隐式全局依赖 | Unreal Subsystems、自研引擎 |
| 事件总线 / 消息架构 | 模块通过事件、信号或消息通信 | 降低直接引用 | 调用链、顺序和重入不直观 | Godot Signals、Defold Messages、O3DE EBus |
| 可视化脚本架构 | 用节点图描述控制流、行为或材质 | 非程序人员可参与开发 | 大图维护、合并和性能分析困难 | Unreal Blueprint、Unity Visual Scripting |

## 游戏循环、并发与渲染

| 架构 | 核心思想 | 适用场景 | 主要注意点 |
|------|----------|----------|------------|
| 可变时间步长 | 每帧用真实 `deltaTime` 更新 | 简单动画、低复杂度游戏 | 物理结果随帧率变化 |
| 固定时间步长 | 按固定间隔推进模拟 | 物理、确定性模拟、竞技游戏 | 渲染需插值，慢帧可能积压 |
| 半固定 / 累加器循环 | 累计真实时间并执行零到多次固定更新 | 大多数实时游戏 | 防止“死亡螺旋”，限制追帧次数 |
| 更新与渲染解耦 | 模拟频率和展示频率独立 | 高刷新率、网络游戏 | 快照双缓冲、插值和线程同步 |
| 单线程顺序循环 | 输入、逻辑、物理和渲染依次执行 | 小型游戏、工具、原型 | 简单但难利用多核 |
| 多线程流水线 | 不同帧或阶段在不同线程并行 | 大型引擎、渲染密集场景 | 延迟、资源所有权和同步复杂 |
| Job System / Task Graph | 将工作拆成带依赖的小任务，由线程池调度 | 动画、物理、ECS、资源处理 | 任务粒度、数据竞争和调试 |
| 帧图 / 渲染图 | 声明渲染 Pass 及资源依赖，由系统排序和复用资源 | 现代多 Pass 渲染 | 图构建、资源屏障和后端抽象 |
| 前向渲染 | 按物体直接计算光照并输出 | 移动端、透明物体、少量光源 | 多光源成本高 |
| 延迟渲染 | 先写 G-Buffer，再统一计算光照 | 大量动态光源 | 带宽和显存高，透明与 MSAA 困难 |
| Forward+ / Clustered | 按屏幕块或空间簇筛选光源后前向着色 | 移动端、VR、大量光源 | 光源分桶和 GPU 数据管理 |
| GPU Driven Rendering | GPU 完成剔除、批次生成和间接绘制 | 海量实例、开放世界 | 平台能力和调试门槛高 |
| 空间分区 | 用四叉树、八叉树、BVH 或网格减少查询范围 | 碰撞、可见性、AI、AOI | 动态更新和内存开销 |
| 世界流式加载 | 按区域或优先级异步加载场景和资源 | 开放世界、无缝地图 | IO、依赖、卸载和状态持久化 |
| 资源管线 | 导入、转换、依赖跟踪、缓存并打包源资源 | 编辑器型引擎和大型项目 | GUID、增量构建和版本兼容 |
| Headless Runtime | 去除图形和编辑器，仅运行模拟或工具逻辑 | 专用服务器、自动测试 | 客户端专属代码必须隔离 |

## 多人游戏网络架构

| 架构 | 核心思想 | 适用场景 | 主要代价 |
|------|----------|----------|----------|
| 本地同屏 | 所有玩家共享一个进程和世界状态 | 派对、格斗、本地合作 | 输入、镜头和资源共享规则复杂 |
| Peer-to-Peer | 玩家节点直接交换数据 | 小规模合作、低服务器成本场景 | NAT、安全、作弊和节点离线 |
| Listen Server | 一名玩家同时作为客户端和服务器 | 小规模房间制游戏 | 主机优势和主机迁移 |
| 专用权威服务器 | 服务端裁决世界状态，客户端提交输入 | MMO、FPS、竞技游戏 | 服务器成本和延迟补偿 |
| 客户端权威 | 客户端决定部分状态，服务端接受或校验 | 低风险交互、休闲玩法 | 更容易作弊和产生冲突 |
| 状态同步 / Snapshot | 服务端周期发送世界快照，客户端插值展示 | FPS、动作游戏 | 带宽、压缩和抖动处理 |
| 输入同步 / Lockstep | 各端交换输入并确定性推进相同模拟 | RTS、棋类 | 最慢端限制进度，必须保证确定性 |
| Rollback Netcode | 先预测本地输入，迟到输入到达后回滚重演 | 格斗、强对抗动作游戏 | 状态快照、重演成本和副作用隔离 |
| 客户端预测 + 服务端校正 | 客户端立即模拟，服务端结果到达后对账 | FPS、运动控制 | 误差平滑和双端一致性 |
| 延迟补偿 | 服务端按历史状态重建玩家操作时刻 | 射击命中判定 | 历史缓存和公平性策略 |
| 兴趣管理 / AOI | 只同步玩家附近或相关实体 | MMO、开放世界 | 空间索引、订阅切换和突发流量 |
| 房间 / 实例架构 | 每局游戏是独立进程、容器或逻辑实例 | MOBA、吃鸡、对战平台 | 匹配、调度和回收 |
| Zone / Shard | 世界按区域或玩家群切分到不同节点 | MMO、大世界 | 跨区迁移、热点和全局一致性 |
| Relay / 转发架构 | 由中继服务转发玩家数据 | 隐藏地址、改善连通性 | 中继成本和额外网络跳数 |

## 主流引擎架构映射

| 引擎 / 框架 | 主要架构特征 | 更适合 |
|-------------|--------------|--------|
| Unity | GameObject-Component；可选 Entities ECS、Job System、Burst；Package 扩展 | 2D/3D、移动端、XR、跨平台商业项目 |
| Unreal Engine | UObject、Actor-Component、Gameplay Framework、插件、Task Graph、网络复制 | 高画质 3D、主机、AAA、虚拟制作 |
| Godot | Node-Scene 树、Signal、Resource、脚本与原生扩展 | 独立游戏、2D/3D、开源定制 |
| Cocos Creator | Node-Component 场景图、TypeScript、原生/网页多后端 | 2D、移动端、小游戏、Web |
| O3DE | Entity-Component、EBus、Gem 微内核、数据驱动工具链 | 大型 3D、引擎定制 |
| Bevy | Rust ECS、Schedule/System、Plugin、数据驱动 | ECS/DOD 项目、Rust 生态 |
| Defold | Collection、Game Object、Component、消息传递 | 轻量 2D、移动端、Web |
| GameMaker | Object、Room、Event、脚本 | 2D 和快速原型 |
| MonoGame / FNA | 框架式游戏循环，开发者自行组织对象和系统 | 代码主导、完全控制的 2D/3D 项目 |
| libGDX | Application 生命周期、Screen、模块化跨平台后端 | Java/Kotlin 跨平台游戏 |
| LÖVE | 回调式主循环、模块化 Lua API | Lua 2D 游戏、教学和原型 |
| Phaser / PixiJS | 场景/对象系统或渲染库 + Web 游戏循环 | 浏览器 2D 游戏和互动内容 |

## 常见组合

| 场景 | 推荐组合 |
|------|----------|
| 独立游戏 | GameObject-Component 或 Node-Scene + 半固定循环 + 数据驱动 |
| 海量单位模拟 | ECS + DOD + Job System + 空间分区 |
| 竞技动作游戏 | 固定模拟 + 权威服务器 + 客户端预测 + Rollback 或延迟补偿 |
| MMO / 大世界 | 权威服务器 + AOI + Zone/Shard + 世界流式加载 |
| 可深度定制引擎 | 原生核心 + 脚本层 + 微内核插件 + 资源管线 |

## 参考资料

- [Unity Entities / ECS](https://docs.unity3d.com/Packages/com.unity.entities@latest)
- [Unreal Engine Gameplay Framework](https://dev.epicgames.com/documentation/unreal-engine/gameplay-framework-in-unreal-engine)
- [Godot Nodes and Scenes](https://docs.godotengine.org/en/stable/getting_started/step_by_step/nodes_and_scenes.html)
- [Bevy ECS](https://bevyengine.org/learn/quick-start/getting-started/ecs/)
- *Game Programming Patterns* — Robert Nystrom
