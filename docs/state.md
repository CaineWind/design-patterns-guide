# 状态模式 (State)

## 简介

状态模式是一种行为型设计模式，它允许对象在其内部状态改变时改变自身的行为，看起来就像修改了对象的类一样。状态模式将不同状态下的行为封装到独立的状态类中，避免了大量的条件判断语句。

## 解决的问题

当对象的行为取决于其状态，且运行时状态会频繁切换时：

- 使用 `if/else` 或 `switch` 判断状态会导致代码臃肿且难以维护
- 新增状态需要修改已有的条件逻辑，违反开闭原则
- 状态转移逻辑散布在多处，难以追踪

状态模式将每种状态的行为和转移逻辑封装到独立的类中，使状态切换清晰可控。

## 结构

| 角色 | 职责 |
|------|------|
| **State（状态接口）** | 定义某种状态下的行为接口 |
| **ConcreteState（具体状态）** | 实现该状态下的具体行为和状态转移 |
| **Context（上下文/状态机）** | 持有当前状态对象，将行为委托给当前状态 |

## 代码实现

```javascript
class State {
  handle(context) {}
}

class StandingState extends State {
  handle(context) {
    console.log("Standing...");
    context.setState(new RunningState());
  }
}

class RunningState extends State {
  handle(context) {
    console.log("Running...");
    context.setState(new StandingState());
  }
}

class StateMachine {
  constructor() {
    this.state = new StandingState();
  }

  setState(state) {
    this.state = state;
  }

  update() {
    this.state.handle(this);
  }
}

// 使用方式
const context = new StateMachine();
context.update();
context.update();
context.update();
```

**要点说明：**

- `State` 定义了状态行为接口 `handle(context)`，接收上下文以便触发状态切换。
- `StandingState` 执行"站立"行为后，将状态切换为 `RunningState`。
- `RunningState` 执行"奔跑"行为后，切回 `StandingState`。
- `StateMachine` 是上下文，持有当前状态并将 `update()` 委托给当前状态处理。
- 新增状态（如跳跃、攻击）只需添加新类，无需修改已有状态代码。

## 运行结果

```
Standing...
Running...
Standing...
```

## 适用场景

- 游戏角色的状态机（站立、跑步、跳跃、攻击、死亡）
- 订单系统的状态流转（待支付 → 已支付 → 已发货 → 已完成）
- TCP 连接状态管理（监听、已建立、关闭中）
- 编辑器的模式切换（普通模式、插入模式、可视模式）
