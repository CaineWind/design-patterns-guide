# 工厂模式 (Factory)

## 简介

工厂模式是一种创建型设计模式，它定义一个用于创建对象的接口，将对象的实例化逻辑集中到一个工厂类中，客户端无需关心具体对象的创建细节。

## 解决的问题

当系统中存在多种类型的对象需要创建时，如果客户端直接使用 `new` 来实例化，会导致：

- 客户端代码与具体类强耦合
- 新增类型时需要修改所有创建该对象的位置
- 创建逻辑分散，难以统一管理

工厂模式将创建逻辑封装到独立的工厂中，客户端只需传入类型标识即可获得正确的实例。

## 结构

| 角色 | 职责 |
|------|------|
| **Product（产品接口）** | 定义产品的公共接口 |
| **ConcreteProduct（具体产品）** | 实现产品接口的具体类 |
| **Factory（工厂）** | 根据参数决定创建哪种具体产品 |

## 代码实现

```javascript
class Monster {
  attack() {}
}

class Goblin extends Monster {
  attack() { return "Goblin attacks!"; }
}

class Troll extends Monster {
  attack() { return "Troll attacks!"; }
}

class EnemyFactory {
  static createEnemy(type) {
    switch (type) {
      case "goblin":
        return new Goblin();
      case "troll":
        return new Troll();
      default:
        throw new Error("Unknown enemy type");
    }
  }
}

// 使用方式
const enemy = EnemyFactory.createEnemy("goblin");
console.log(enemy.attack());
```

**要点说明：**

- `Monster` 作为抽象基类定义了产品接口 `attack()`。
- `Goblin` 和 `Troll` 是具体产品，各自实现攻击行为。
- `EnemyFactory.createEnemy()` 是工厂方法，通过 `type` 参数决定实例化哪个具体类。
- 客户端只依赖 `Monster` 接口和 `EnemyFactory`，不直接依赖具体类。

## 运行结果

```
Goblin attacks!
```

## 适用场景

- 游戏中动态生成不同类型的敌人、道具、NPC
- 根据配置文件创建不同的数据库连接
- UI 框架中根据类型渲染不同组件
- 任何需要在运行时根据条件决定创建哪种对象的场景
