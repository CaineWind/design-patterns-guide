# 装饰器模式 (Decorator)

## 简介

装饰器模式是一种结构型设计模式，它允许在不修改原有对象结构的情况下，动态地为对象添加新的职责（功能）。装饰器通过包装原对象来扩展其行为，是继承的一种灵活替代方案。

## 解决的问题

当需要为对象添加功能时，使用继承会导致：

- 类爆炸问题：每种功能组合都需要一个子类
- 静态绑定：功能在编译时确定，无法动态增减
- 违反单一职责原则：一个子类可能同时承担多种扩展

装饰器模式通过层层包装的方式，让功能可以自由叠加和组合。

## 结构

| 角色 | 职责 |
|------|------|
| **Component（组件接口）** | 定义对象的核心接口 |
| **ConcreteComponent（具体组件）** | 被装饰的原始对象 |
| **Decorator（装饰器基类）** | 持有 Component 引用，转发接口调用 |
| **ConcreteDecorator（具体装饰器）** | 在转发调用的基础上添加新行为 |

## 代码实现

```javascript
class Player {
  attack() {
    return "Player attacks!";
  }
}

class PowerUpDecorator {
  constructor(player) {
    this.player = player;
  }

  attack() {
    return this.player.attack();
  }
}

class FirePowerUp extends PowerUpDecorator {
  attack() {
    return `${super.attack()} with fire!`;
  }
}

class IcePowerUp extends PowerUpDecorator {
  attack() {
    return `${super.attack()} with ice!`;
  }
}

// 使用方式
let player = new Player();
console.log(player.attack());

player = new FirePowerUp(player);
console.log(player.attack());

player = new IcePowerUp(player);
console.log(player.attack());
```

**要点说明：**

- `Player` 是被装饰的原始对象，提供基础的 `attack()` 行为。
- `PowerUpDecorator` 是装饰器基类，持有被装饰对象的引用并透传调用。
- `FirePowerUp` 和 `IcePowerUp` 在调用原始方法的基础上追加了额外效果。
- 装饰器可以层层叠加：先加火属性，再加冰属性，效果累积。
- 装饰过程在运行时动态进行，无需预定义所有组合。

## 运行结果

```
Player attacks!
Player attacks! with fire!
Player attacks! with fire! with ice!
```

## 适用场景

- 游戏中的 Buff/增益系统（叠加效果）
- Java I/O 流（BufferedInputStream 包装 FileInputStream）
- HTTP 中间件（日志 → 认证 → 压缩 → 处理器）
- 动态为 UI 组件添加边框、滚动条、阴影等视觉效果
