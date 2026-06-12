# 外观模式 (Facade)

## 简介

外观模式是一种结构型设计模式，它为一个复杂的子系统提供一个简化的统一接口。客户端通过外观类与子系统交互，而无需了解子系统内部的复杂性。

## 解决的问题

当系统由多个相互协作的子系统组成时：

- 客户端需要了解每个子系统的接口和调用顺序
- 子系统之间的耦合暴露给了外部调用者
- 简单的操作也需要多步骤的复杂调用

外观模式提供一个高层接口，将多个子系统的协作封装为一次简单调用。

## 结构

| 角色 | 职责 |
|------|------|
| **Facade（外观）** | 封装子系统的复杂性，提供简化接口 |
| **Subsystem（子系统）** | 各自独立的功能模块，不感知外观的存在 |

## 代码实现

```javascript
class SoundSystem {
  playSound() {
    return "Playing sound";
  }
}

class RenderSystem {
  renderGraphics() {
    return "Rendering graphics";
  }
}

class PhysicsSystem {
  updatePhysics() {
    return "Updating physics";
  }
}

class GameFacade {
  constructor() {
    this.soundSystem = new SoundSystem();
    this.renderSystem = new RenderSystem();
    this.physicsSystem = new PhysicsSystem();
  }

  play() {
    console.log(this.soundSystem.playSound());
    console.log(this.renderSystem.renderGraphics());
    console.log(this.physicsSystem.updatePhysics());
  }
}

// 使用方式
const game = new GameFacade();
game.play();
```

**要点说明：**

- `SoundSystem`、`RenderSystem`、`PhysicsSystem` 是三个独立的子系统，各自负责不同的职责。
- `GameFacade` 将三个子系统的初始化和调用顺序封装在 `play()` 方法中。
- 客户端只需调用 `game.play()`，无需知道内部有哪些子系统、以什么顺序执行。
- 子系统仍然可以独立使用，外观只是提供了一个便捷入口。

## 运行结果

```
Playing sound
Rendering graphics
Updating physics
```

## 适用场景

- 游戏引擎的主循环（音频、渲染、物理、网络的统一调度）
- 编译器的 `compile()` 方法（词法分析 → 语法分析 → 代码生成）
- 电商系统的下单接口（库存扣减 → 支付 → 物流创建）
- 任何需要简化复杂子系统调用的场景
