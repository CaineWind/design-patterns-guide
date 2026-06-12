# 组合模式 (Composite)

## 简介

组合模式是一种结构型设计模式，它将对象组织成树形结构以表示"部分-整体"的层次关系。组合模式使得客户端对单个对象和组合对象的使用具有一致性——无论是叶子节点还是容器节点，都通过相同的接口操作。

## 解决的问题

在以下场景中，对象天然具有层级关系：

- 游戏世界中的场景树（世界 → 区域 → 角色）
- 文件系统（目录 → 子目录 → 文件）
- UI 组件树（页面 → 容器 → 按钮）

如果对"单个元素"和"元素集合"使用不同的处理方式，客户端代码将充斥条件判断。组合模式通过统一接口消除这种区别。

## 结构

| 角色 | 职责 |
|------|------|
| **Component（组件）** | 声明组合中所有对象的通用接口（如 `display()`、`add()`、`remove()`） |
| **Leaf（叶子节点）** | 没有子节点的末端对象 |
| **Composite（组合节点）** | 包含子节点的容器，实现子节点管理方法 |

## 代码实现

```javascript
class GameObject {
  constructor(name) {
    this.name = name;
    this.children = [];
  }

  add(child) {
    this.children.push(child);
  }

  remove(child) {
    this.children = this.children.filter(c => c !== child);
  }

  display(indent = 0) {
    console.log(`${' '.repeat(indent)}${this.name}`);
    this.children.forEach(child => child.display(indent + 2));
  }
}

class Player extends GameObject {
  constructor(name) {
    super(name);
  }
}

class Enemy extends GameObject {
  constructor(name) {
    super(name);
  }
}

// 使用方式
const root = new GameObject("Game World");
const player = new Player("Player 1");
const enemy1 = new Enemy("Enemy 1");
const enemy2 = new Enemy("Enemy 2");

root.add(player);
root.add(enemy1);
root.add(enemy2);

root.display();
```

**要点说明：**

- `GameObject` 既是组件基类又是组合节点，持有 `children` 列表并实现递归 `display()`。
- `Player` 和 `Enemy` 继承自 `GameObject`，既可以作为叶子节点，也可以继续添加子节点（如装备、技能树）。
- `display()` 方法递归遍历整棵树，缩进显示层级关系。
- 无论对树的根节点还是任意子节点调用 `display()`，行为一致。

## 运行结果

```
Game World
  Player 1
  Enemy 1
  Enemy 2
```

## 适用场景

- 游戏引擎中的场景图 (Scene Graph)
- 文件管理器的目录树
- 组织架构图（公司 → 部门 → 员工）
- 前端组件的虚拟 DOM 树
