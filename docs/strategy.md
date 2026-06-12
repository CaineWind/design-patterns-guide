# 策略模式 (Strategy)

## 简介

策略模式是一种行为型设计模式，它定义一系列算法，将每种算法封装到独立的类中，并使它们可以互相替换。策略模式让算法的变化独立于使用它的客户端。

## 解决的问题

当同一操作有多种实现方式，且需要在运行时动态选择时：

- 使用条件语句选择算法会导致代码臃肿，新增算法需要修改原有逻辑
- 算法的实现细节与业务逻辑混杂在一起
- 无法在运行时灵活切换算法

策略模式将每种算法封装为独立的策略对象，上下文只需持有策略接口的引用，即可在运行时自由切换。

## 结构

| 角色 | 职责 |
|------|------|
| **Strategy（策略接口）** | 定义算法的统一接口 |
| **ConcreteStrategy（具体策略）** | 实现某种具体算法 |
| **Context（上下文）** | 持有策略对象，将算法执行委托给当前策略 |

## 代码实现

```javascript
class Strategy {
  execute(data) {}
}

class QuickSort extends Strategy {
  execute(data) {
    console.log("Sorting using quick sort");
  }
}

class BubbleSort extends Strategy {
  execute(data) {
    console.log("Sorting using bubble sort");
  }
}

class Context {
  constructor(strategy) {
    this.strategy = strategy;
  }

  setStrategy(strategy) {
    this.strategy = strategy;
  }

  executeStrategy(data) {
    this.strategy.execute(data);
  }
}

// 使用方式
const data = [5, 3, 8, 1];

const context = new Context(new QuickSort());
context.executeStrategy(data);

context.setStrategy(new BubbleSort());
context.executeStrategy(data);
```

**要点说明：**

- `Strategy` 定义了算法接口 `execute(data)`。
- `QuickSort` 和 `BubbleSort` 是两种具体策略，各自封装不同的排序逻辑。
- `Context` 持有当前策略的引用，通过 `setStrategy()` 可在运行时切换算法。
- 新增排序算法（如归并排序）只需添加新的策略类，不修改已有代码。
- 策略模式与状态模式的区别：策略模式由客户端主动选择算法；状态模式由对象内部根据状态自动切换行为。

## 运行结果

```
Sorting using quick sort
Sorting using bubble sort
```

## 适用场景

- 排序算法的动态选择
- 支付系统中选择不同支付方式（微信、支付宝、信用卡）
- 压缩工具支持多种压缩算法（ZIP、GZIP、LZ4）
- 游戏 AI 的行为策略切换（巡逻、追击、逃跑）
- 表单验证规则的动态组合
