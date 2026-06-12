# 单例模式 (Singleton)

## 简介

单例模式是一种创建型设计模式，它确保一个类在整个应用程序生命周期中只存在一个实例，并提供一个全局访问点来获取该实例。

## 解决的问题

在软件系统中，某些对象只应存在一个实例。例如：

- 游戏中的全局管理器（GameManager）
- 应用程序的配置中心
- 日志记录器
- 数据库连接池

如果允许这类对象被多次实例化，可能导致资源浪费、状态不一致或逻辑冲突。单例模式通过控制实例化过程来避免这些问题。

## 结构

| 角色 | 职责 |
|------|------|
| **Singleton（单例类）** | 持有自身唯一实例的静态引用，提供静态方法获取该实例，隐藏构造逻辑 |

## 代码实现

```javascript
class GameManager {
  static #instance_ = null;

  #score_ = 0;

  constructor() {
    this.#score_ = 0;
  }

  test() {
    console.log('Hello from GameManager singleton instance!');
  }

  static Instance() {
    return GameManager.#instance_;
  }

  static Initialize() {
    if (!GameManager.#instance_) {
      GameManager.#instance_ = new GameManager();
    }
  }
}

// 使用方式
GameManager.Initialize();
GameManager.Instance().test();
```

**要点说明：**

- `#instance_` 使用私有静态字段存储唯一实例，外部无法直接访问或篡改。
- `Initialize()` 方法负责创建实例，内部通过判空逻辑保证只创建一次。
- `Instance()` 方法作为全局访问点，返回已创建的实例。
- 理想情况下，构造函数应设为私有（JavaScript 目前无法完全实现私有构造函数，但可通过约定或 Symbol 技巧限制）。

## 运行结果

```
Hello from GameManager singleton instance!
```

## 适用场景

- 全局状态管理（游戏分数、用户会话）
- 硬件资源访问（打印机池、GPU 上下文）
- 配置信息的集中读取
- 线程池、缓存管理器
