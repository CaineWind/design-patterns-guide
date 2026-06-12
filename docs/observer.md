# 观察者模式 (Observer)

## 简介

观察者模式是一种行为型设计模式，它定义了对象之间一对多的依赖关系：当一个对象（主题）的状态发生变化时，所有依赖它的对象（观察者）都会自动收到通知并更新。

## 解决的问题

在以下场景中，对象之间需要保持同步但又不应紧密耦合：

- UI 界面需要响应数据变化（数据改变 → 视图刷新）
- 事件系统（用户操作 → 多个处理器响应）
- 消息推送（发布者发布消息 → 所有订阅者接收）

如果让依赖方主动轮询状态，既浪费资源又可能错过更新。观察者模式让状态变化主动推送给所有关心它的对象。

## 结构

| 角色 | 职责 |
|------|------|
| **Subject（主题/被观察者）** | 维护观察者列表，状态变化时通知所有观察者 |
| **Observer（观察者）** | 定义接收通知的接口 `update()` |
| **ConcreteObserver（具体观察者）** | 实现具体的响应逻辑 |

## 代码实现

```javascript
class Subject {
  constructor() {
    this.observers = [];
  }

  addObserver(observer) {
    this.observers.push(observer);
  }

  notifyObservers() {
    this.observers.forEach(observer => observer.update());
  }
}

class Observer {
  constructor(name) {
    this.name = name;
  }

  update() {
    console.log(`${this.name} updated`);
  }
}

// 使用方式
const subject = new Subject();
const observer1 = new Observer('Observer 1');
const observer2 = new Observer('Observer 2');

subject.addObserver(observer1);
subject.addObserver(observer2);

subject.notifyObservers();
```

**要点说明：**

- `Subject` 维护一个观察者列表，提供注册（`addObserver`）和通知（`notifyObservers`）方法。
- `Observer` 定义了 `update()` 接口，当收到通知时执行自己的逻辑。
- 主题与观察者之间是松耦合关系：主题不知道观察者的具体类型，观察者也可以随时注册或取消。
- 在实际应用中，`notifyObservers()` 通常在主题状态改变时自动调用（如属性的 setter 中）。

## 运行结果

```
Observer 1 updated
Observer 2 updated
```

## 适用场景

- 前端框架的响应式系统（Vue 的数据绑定、React 的状态更新）
- DOM 事件监听机制
- 消息中间件的发布/订阅模型
- 游戏中的成就系统（各模块监听玩家行为事件）
- MVC 架构中 Model 通知 View 更新
