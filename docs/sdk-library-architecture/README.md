# SDK 与库的架构

[返回项目总览](../../README.md)

本章关注 SDK 和库如何封装能力、设计调用方式、提供扩展点并跨平台交付。一个成熟 SDK 通常会组合模块化包、Facade、Core + Adapter、Middleware、代码生成和兼容性策略。

## 目录

- [包与模块组织](#包与模块组织)
- [API 与控制流风格](#api-与控制流风格)
- [扩展、交付与兼容](#扩展交付与兼容)
- [代表性 SDK 与库](#代表性-sdk-与库)
- [常见组合](#常见组合)

## 包与模块组织

| 架构 | 核心思想 | 优点 | 主要代价 / 代表形式 |
|------|----------|------|---------------------|
| 单体库 | 所有功能由一个包或二进制提供 | 安装和版本简单 | 体积大、耦合高 |
| 模块化包 | 按功能拆成可独立选择的包 | 按需安装、发布边界清晰 | 版本矩阵复杂；AWS SDK v3、AndroidX |
| Core + Adapter | 稳定核心定义协议，平台和第三方能力由适配器实现 | 核心可测试、易跨平台 | 适配器数量多 |
| API / SPI 分离 | 使用方依赖 API，提供方实现 SPI | 可替换实现，适合生态扩展 | 服务发现和兼容治理；JDBC、SLF4J |
| 微内核 + Plugin | 内核提供生命周期和扩展点，插件提供具体能力 | 高度可扩展和可裁剪 | 插件隔离、加载顺序和版本冲突 |
| 分层库 | 公共 API、领域层、实现层、平台层逐层依赖 | 内部职责清晰 | 包装层可能增加认知成本 |
| 平台抽象层 | 统一接口屏蔽操作系统、图形或硬件差异 | 一套逻辑支持多平台 | 受最小公共能力限制；SDL |
| 多后端架构 | 同一前端选择不同执行、存储或渲染后端 | 灵活适配部署环境 | 后端语义难完全统一 |
| Monorepo 多包 | 多个包同仓开发、统一测试和发布编排 | 跨包重构方便 | CI 成本和发布工具复杂 |
| 运行时 + Binding | 核心运行时通过语言绑定暴露给多语言 | 复用高性能核心 | FFI、内存所有权和错误映射复杂 |
| API 与实现分离 | 接口包稳定，具体实现可替换或延迟加载 | 降低编译和运行耦合 | 装配更复杂 |

## API 与控制流风格

| 架构 / 风格 | 调用模型 | 适用场景 | 代表实现 |
|-------------|----------|----------|----------|
| 过程式 API | 函数 + 句柄操作状态 | C ABI、底层系统、简单工具 | POSIX、SQLite C API、OpenGL |
| 面向对象 API | Client、Service、Resource 对象封装状态和行为 | 业务 SDK、GUI、领域库 | AWS/Azure SDK Client、Qt |
| Facade | 用少量高层入口隐藏子系统协作 | 简化复杂 SDK 的常见流程 | 支付、媒体、云服务 SDK |
| Fluent / Builder | 链式调用逐步构造请求或配置 | 可选参数多、强调可读性 | HTTP Client、查询构造器 |
| 声明式 API | 描述目标状态，由框架负责实现 | UI、基础设施、数据查询 | React、SQL、Terraform |
| DSL | 用专用语法表达领域意图 | 路由、构建、测试、查询 | Gradle DSL、RSpec |
| Callback / Listener | 操作完成或事件发生时回调调用方 | GUI、设备、网络、异步 I/O | DOM、Node.js EventEmitter |
| Promise / Future | 异步结果是一等值，可组合成功和失败流程 | 网络、并发、异步任务 | Promise、CompletableFuture |
| Reactive / Observable | 值随时间形成流，订阅者组合转换操作符 | UI 状态、实时数据 | ReactiveX、Kotlin Flow、Combine |
| Iterator / Pull Stream | 消费方主动逐项拉取数据 | 集合、分页、惰性计算 | Iterable、Iterator |
| Push Stream | 生产方持续推送数据并处理背压 | 日志、网络、实时处理 | Node Streams、Reactive Streams |
| Middleware Pipeline | 请求依次经过可组合中间件 | HTTP、RPC、编译、遥测 | Express/Koa、gRPC Interceptor |
| Filter / Codec Pipeline | 数据依次经过解析、转换和编解码步骤 | 媒体、网络、编译器 | FFmpeg、GStreamer、LLVM Pass |
| Hook / Lifecycle | 框架在固定生命周期点调用扩展代码 | 插件、构建、ORM、UI | React Hooks、Webpack Plugins |
| Dependency Injection | 容器或组合根注入具体实现 | 大型框架和可测试业务库 | Spring、.NET DI、Dagger |
| Convention over Configuration | 通过目录、命名和类型约定减少配置 | Web 框架、ORM | Rails、Spring Boot |
| Annotation / Reflection | 元数据驱动注册、序列化、路由或注入 | 企业框架和运行时扩展 | Spring、Jackson、.NET Attributes |
| Code Generation | 编译前后生成客户端、绑定或样板代码 | 强类型 API、序列化、跨语言 RPC | OpenAPI、Protocol Buffers |
| Repository / Data Mapper | 用对象接口隔离存储和领域模型 | ORM、业务数据访问 | Hibernate、Entity Framework |
| Command Buffer | 先记录操作，再批量提交执行 | 图形、数据库、远程调用 | Vulkan |
| Computational Graph | 把运算构成图再优化或执行 | AI、数据流、科学计算 | TensorFlow、ONNX Runtime |

## 扩展、交付与兼容

| 架构 | 核心思想 | 适用场景 | 关键风险 |
|------|----------|----------|----------|
| 静态链接库 | 构建时把库并入可执行文件 | 嵌入式、单文件交付 | 体积、升级需重建、许可证 |
| 动态链接库 | 运行时加载共享库 | 系统组件、可插拔后端 | ABI、加载路径、依赖冲突 |
| Header-only / Source-only | 直接分发源码或模板头文件 | C++ 模板库、小型跨平台库 | 编译时间和实现暴露 |
| 稳定 C ABI | 用最小 C 边界连接不同语言和编译器 | 系统 SDK、语言绑定 | 类型表达力和生命周期管理 |
| PImpl / ABI 防火墙 | 公共类隐藏实现布局 | C++ 二进制兼容 SDK | 间接访问和分配成本 |
| Semantic Versioning | 用主、次、补丁版本表达兼容性 | 包生态和公共 API | 必须严格定义行为兼容 |
| Capability Detection | 运行时探测功能而非假设版本 | 浏览器、GPU、设备 SDK | 分支和测试矩阵膨胀 |
| Polyfill / Shim | 为旧平台补齐新 API 或转换接口 | Web 和兼容层 | 行为无法完全一致 |
| Embedded Runtime | SDK 自带解释器、VM 或执行引擎 | 脚本、规则、数据库、AI 推理 | 包体、沙箱和升级 |
| WebAssembly 模块 | 以 WASM 作为可移植沙箱执行单元 | 浏览器、边缘、插件 | Host ABI、调试和平台能力 |
| Client Stub / Proxy | 本地对象把调用编码为远程请求 | RPC、云服务 SDK | 网络失败、延迟和重试语义 |
| Side-by-side Versioning | 多版本运行时或 API 同时存在 | 大型平台、渐进迁移 | 状态共享和体积成本 |

## 代表性 SDK 与库

| SDK / 库 | 架构特征 |
|----------|----------|
| AWS SDK / Azure SDK | 按服务拆包、Client/Command、请求中间件、重试和凭证 Provider |
| Android Jetpack / AndroidX | 模块化包、生命周期感知组件、Repository/ViewModel/Flow |
| React | 声明式组件、组合、单向数据流、Hooks |
| Redux | 单一 Store、Action、纯 Reducer、单向状态变更 |
| RxJS / ReactiveX | Observable、操作符管线、调度器、Push Stream |
| Express / Koa | 轻量核心、路由和 Middleware Pipeline |
| gRPC / Protocol Buffers | IDL 优先、代码生成 Stub、Interceptor、跨语言 RPC |
| OpenTelemetry | API 与 SDK 分离、Provider、Processor、Exporter 插件链 |
| SDL | C API、平台抽象、多后端实现 |
| FFmpeg / GStreamer | Demux、Decode、Filter、Encode、Mux 流水线 |
| TensorFlow / PyTorch | 张量、算子、自动微分、计算图/即时执行、设备后端 |
| LLVM | 稳定 IR、多前端/多后端、Pass Pipeline |
| Qt | 对象模型、Signal/Slot、事件循环、平台抽象、模块化组件 |
| OpenGL / Vulkan | 状态机式 API / 显式资源和命令缓冲 API |

## 常见组合

| 场景 | 推荐组合 |
|------|----------|
| 公共云服务 SDK | 模块化包 + Client/Command + Middleware + 重试 Provider |
| 跨平台系统库 | 稳定 C ABI + Core/Adapter + 平台抽象 + Capability Detection |
| 可扩展框架 | 微内核 + Plugin + API/SPI 分离 + 生命周期 Hook |
| 强类型远程 API | IDL + Code Generation + Client Stub + Interceptor |
| 数据或媒体处理库 | Pipeline + Iterator/Stream + 多后端 |

## 参考资料

- [Azure SDK General Guidelines](https://azure.github.io/azure-sdk/general_introduction.html)
- [Protocol Buffers Documentation](https://protobuf.dev/)
- [OpenTelemetry Specification](https://opentelemetry.io/docs/specs/otel/)
