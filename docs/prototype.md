# 原型模式 (Prototype)

## 简介

原型模式是一种创建型设计模式，它通过复制（克隆）现有对象来创建新对象，而非通过构造函数重新实例化。新对象是原型的副本，可以在此基础上独立修改。

## 解决的问题

在以下场景中，直接使用 `new` 创建对象可能代价高昂或不便：

- 对象初始化过程复杂（涉及大量计算、网络请求、文件读取）
- 需要批量创建相似对象，仅有少量属性不同
- 创建逻辑被封装在其他模块中，外部无法直接调用构造函数

原型模式通过 `clone()` 方法让对象自行复制，避免重复执行昂贵的初始化逻辑。

## 结构

| 角色 | 职责 |
|------|------|
| **Prototype（原型接口）** | 声明 `clone()` 方法 |
| **ConcretePrototype（具体原型）** | 实现 `clone()` 方法，返回自身的副本 |

## 代码实现

```javascript
class NPC {
  constructor(health) {
    this.health = health;
  }

  clone() {
    return new NPC(this.health);
  }

  test() {
    return `Monster has ${this.health} HP`;
  }
}

class Goblin extends NPC {
  constructor(name, health) {
    super(health);
    this.name = name;
  }

  clone() {
    return new Goblin(this.name, this.health);
  }

  test() {
    return `Goblin ${this.name} has ${this.health} HP`;
  }
}

// 使用方式
const originalGoblin = new Goblin('Bill', 100);
const clonedGoblin = originalGoblin.clone();
clonedGoblin.name = 'Ted';

console.log(originalGoblin.test());
console.log(clonedGoblin.test());
```

**要点说明：**

- 每个类实现自己的 `clone()` 方法，确保所有属性被正确复制。
- 克隆得到的对象与原对象完全独立，修改克隆体不会影响原型。
- 对于包含引用类型属性的对象，需注意实现深拷贝（本例中仅包含基本类型）。

## 运行结果

```
Goblin Bill has 100 HP
Goblin Ted has 100 HP
```

## 适用场景

- 游戏中批量生成相似的 NPC 或敌人
- 文档编辑器中复制粘贴复杂图形对象
- 配置对象的快照与回滚
- 任何需要快速创建已有对象副本的场景
