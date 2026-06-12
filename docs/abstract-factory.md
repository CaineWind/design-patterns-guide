# 抽象工厂模式 (Abstract Factory)

## 简介

抽象工厂模式是一种创建型设计模式，它提供一个接口用于创建一系列相关或相互依赖的对象，而无需指定它们的具体类。与简单工厂模式不同，抽象工厂关注的是**一族产品**的创建，而非单个产品。

## 解决的问题

当系统需要创建多个相互关联的对象族时，例如：

- 游戏中"兽人阵营"包含兽人战士和兽人法师，"精灵阵营"包含精灵战士和精灵法师
- UI 框架中的亮色主题和暗色主题各自包含一套按钮、输入框、卡片组件

如果每种组合都单独创建，代码将难以维护且容易混淆不同族的对象。抽象工厂通过为每个产品族提供一个工厂，确保创建出的对象始终属于同一族。

## 结构

| 角色 | 职责 |
|------|------|
| **AbstractFactory（抽象工厂）** | 声明创建一族产品的接口 |
| **ConcreteFactory（具体工厂）** | 实现创建某一族具体产品的逻辑 |
| **AbstractProduct（抽象产品）** | 定义产品的公共接口 |
| **ConcreteProduct（具体产品）** | 属于某一族的具体产品实现 |

## 代码实现

```javascript
class NPC {
  attack() {}
}

class OrcWarrior extends NPC {
  attack() { return "Orc Warrior attacks!"; }
}

class OrcMage extends NPC {
  attack() { return "Orc Mage casts a spell!"; }
}

class ElfWarrior extends NPC {
  attack() { return "Elf Warrior attacks!"; }
}

class ElfMage extends NPC {
  attack() { return "Elf Mage casts a spell!"; }
}

class AbstractFactory {
  create() {}
}

class OrcFactory extends AbstractFactory {
  create(type) {
    switch (type) {
      case "warrior":
        return new OrcWarrior();
      case "mage":
        return new OrcMage();
      default:
        throw new Error("Unknown enemy type");
    }
  }
}

class ElfFactory extends AbstractFactory {
  create(type) {
    switch (type) {
      case "warrior":
        return new ElfWarrior();
      case "mage":
        return new ElfMage();
      default:
        throw new Error("Unknown enemy type");
    }
  }
}

// 使用方式
function createArmy(factory) {
  const warrior = factory.create('warrior');
  const mage = factory.create('mage');
  console.log(warrior.attack());
  console.log(mage.attack());
}

const orcFactory = new OrcFactory();
createArmy(orcFactory);

const elfFactory = new ElfFactory();
createArmy(elfFactory);
```

**要点说明：**

- `AbstractFactory` 定义了创建产品的统一接口 `create(type)`。
- `OrcFactory` 和 `ElfFactory` 各自负责创建属于自己阵营的角色。
- `createArmy()` 函数接收任意工厂实例，通过统一接口创建角色，完全不关心具体阵营。
- 新增阵营时只需添加新的工厂类，无需修改已有代码，符合开闭原则。

## 运行结果

```
Orc Warrior attacks!
Orc Mage casts a spell!
Elf Warrior attacks!
Elf Mage casts a spell!
```

## 适用场景

- 游戏中不同阵营/种族的角色生成系统
- 跨平台 UI 组件库（Windows 风格 / macOS 风格）
- 数据库访问层支持多种数据库（MySQL 工厂 / PostgreSQL 工厂）
- 任何需要保证一组对象在逻辑上一致的场景
