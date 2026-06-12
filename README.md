# 设计模式 (Design Patterns)

通过 JavaScript 代码示例，系统学习 GoF 经典设计模式。

每种模式包含：概念定义、解决的问题、结构说明、完整代码实现、运行结果及适用场景。

## 目录

### 创建型模式 (Creational Patterns)

> 关注对象的创建机制，在合适的场景下以合适的方式创建对象。

| 模式 | 核心思想 |
|------|----------|
| [单例模式 (Singleton)](docs/singleton.md) | 确保一个类只有一个实例，并提供全局访问点 |
| [工厂模式 (Factory)](docs/factory.md) | 将对象创建逻辑集中到工厂类，客户端无需关心具体类 |
| [抽象工厂模式 (Abstract Factory)](docs/abstract-factory.md) | 创建一族相关对象，保证产品之间的一致性 |
| [原型模式 (Prototype)](docs/prototype.md) | 通过克隆现有对象来创建新对象，避免重复初始化 |

### 结构型模式 (Structural Patterns)

> 关注类和对象的组合方式，形成更大的结构。

| 模式 | 核心思想 |
|------|----------|
| [适配器模式 (Adapter)](docs/adapter.md) | 将不兼容的接口转换为客户端期望的接口 |
| [组合模式 (Composite)](docs/composite.md) | 将对象组织成树形结构，统一处理单个对象和组合对象 |
| [装饰器模式 (Decorator)](docs/decorator.md) | 动态为对象添加职责，替代继承实现功能扩展 |
| [外观模式 (Facade)](docs/facade.md) | 为复杂子系统提供简化的统一接口 |

### 行为型模式 (Behavioral Patterns)

> 关注对象之间的职责分配和通信方式。

| 模式 | 核心思想 |
|------|----------|
| [命令模式 (Command)](docs/command.md) | 将请求封装为对象，支持排队、日志和撤销 |
| [观察者模式 (Observer)](docs/observer.md) | 定义一对多依赖，状态变化时自动通知所有观察者 |
| [状态模式 (State)](docs/state.md) | 将状态相关行为封装到独立类中，状态切换即行为切换 |
| [策略模式 (Strategy)](docs/strategy.md) | 将算法封装为可互换的策略对象，运行时动态选择 |

## 如何阅读

1. 从 [README 目录](#目录) 中选择感兴趣的模式
2. 阅读模式文档的「简介」和「解决的问题」部分，理解其应用场景
3. 研读「代码实现」部分，结合要点说明理解设计意图
4. 参考「适用场景」部分，将模式映射到自己的实际项目中

## 参考资料

- *Design Patterns: Elements of Reusable Object-Oriented Software* — Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides (GoF)
- *Head First Design Patterns* — Eric Freeman, Elisabeth Robson

## 许可证

[MIT](LICENSE)
