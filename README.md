# 设计模式与软件架构指南

这是一个面向中文读者的软件设计知识库，通过 JavaScript 示例讲解 GoF 经典设计模式，并按领域整理游戏引擎、SDK/库、服务端、应用端和 Web 经典架构。

根 README 只提供内容导航。完整说明、优缺点、代表实现和组合建议均放在对应的二级文档中。

## 内容导航

| 知识层级 | 关注点 | 入口 |
|----------|--------|------|
| 设计模式 | 类、对象和模块之间的局部协作方式 | [GoF 设计模式](#gof-设计模式) |
| 游戏引擎架构 | 世界组织、主循环、渲染并发和多人网络 | [进入专题](docs/game-engine-architecture/README.md) |
| SDK 与库架构 | 包结构、API 风格、扩展机制和兼容交付 | [进入专题](docs/sdk-library-architecture/README.md) |
| 服务端架构 | 服务边界、通信、数据一致性、伸缩和韧性 | [进入专题](docs/server-architecture/README.md) |
| 应用端架构 | UI 模式、状态流、模块组织和跨平台运行 | [进入专题](docs/application-architecture/README.md) |
| Web 经典架构 | Web 运行模式、3D/医学/科学可视化和 Agent Web 应用 | [进入专题](docs/web-architecture/README.md) |

## GoF 设计模式

每篇模式文档包含概念定义、解决的问题、结构说明、完整 JavaScript 示例、运行结果和适用场景。

### 创建型模式

> 关注对象的创建机制，在合适的场景下以合适的方式创建对象。

| 模式 | 核心思想 |
|------|----------|
| [单例模式 (Singleton)](docs/singleton.md) | 确保一个类只有一个实例，并提供全局访问点 |
| [工厂模式 (Factory)](docs/factory.md) | 将对象创建逻辑集中到工厂类，客户端无需关心具体类 |
| [抽象工厂模式 (Abstract Factory)](docs/abstract-factory.md) | 创建一族相关对象，保证产品之间的一致性 |
| [原型模式 (Prototype)](docs/prototype.md) | 通过克隆现有对象来创建新对象，避免重复初始化 |

### 结构型模式

> 关注类和对象的组合方式，形成更大的结构。

| 模式 | 核心思想 |
|------|----------|
| [适配器模式 (Adapter)](docs/adapter.md) | 将不兼容的接口转换为客户端期望的接口 |
| [组合模式 (Composite)](docs/composite.md) | 将对象组织成树形结构，统一处理单个对象和组合对象 |
| [装饰器模式 (Decorator)](docs/decorator.md) | 动态为对象添加职责，替代继承实现功能扩展 |
| [外观模式 (Facade)](docs/facade.md) | 为复杂子系统提供简化的统一接口 |

### 行为型模式

> 关注对象之间的职责分配和通信方式。

| 模式 | 核心思想 |
|------|----------|
| [命令模式 (Command)](docs/command.md) | 将请求封装为对象，支持排队、日志和撤销 |
| [观察者模式 (Observer)](docs/observer.md) | 定义一对多依赖，状态变化时自动通知所有观察者 |
| [状态模式 (State)](docs/state.md) | 将状态相关行为封装到独立类中，状态切换即行为切换 |
| [策略模式 (Strategy)](docs/strategy.md) | 将算法封装为可互换的策略对象，运行时动态选择 |

## 架构专题

五个专题采用相同的阅读结构：领域边界、架构分类、代表实现、主要代价、常见组合和参考资料。

| 专题 | 包含内容 |
|------|----------|
| [游戏引擎架构](docs/game-engine-architecture/README.md) | 对象与世界组织、游戏循环、并发渲染、网络同步、主流引擎映射 |
| [SDK 与库的架构](docs/sdk-library-architecture/README.md) | 包与模块、API 控制流、插件扩展、二进制兼容、主流库映射 |
| [服务端架构](docs/server-architecture/README.md) | 系统拆分、业务组织、通信执行、数据一致性、云原生与韧性 |
| [应用端架构](docs/application-architecture/README.md) | MVC/MVVM/MVI、模块组织、Web 渲染、移动与桌面跨平台 |
| [Web 经典架构](docs/web-architecture/README.md) | MPA/SPA/SSR、React、Vue、Svelte、Three.js、Babylon.js、Cornerstone3D、VTK.js、VS Code、DeepSeek Harness |

## 推荐阅读路径

1. 从一个具体的 [GoF 设计模式](#gof-设计模式)开始，理解局部对象协作。
2. 根据开发领域进入对应的[架构专题](#架构专题)，识别系统级约束。
3. 先明确部署边界、依赖方向、通信方式、状态归属和一致性要求。
4. 从最简单的可行结构开始，只在业务、团队或性能指标提供证据时增加复杂度。
5. 使用 ADR（Architecture Decision Record）记录关键选择、代价和退出方案。

## 参考资料

- *Design Patterns: Elements of Reusable Object-Oriented Software* — Erich Gamma、Richard Helm、Ralph Johnson、John Vlissides
- *Head First Design Patterns* — Eric Freeman、Elisabeth Robson
- *Patterns of Enterprise Application Architecture* — Martin Fowler
- *Game Programming Patterns* — Robert Nystrom
- *Designing Data-Intensive Applications* — Martin Kleppmann
- *Software Architecture: The Hard Parts* — Neal Ford、Mark Richards、Pramod Sadalage、Zhamak Dehghani

## 许可证

[MIT](LICENSE)
