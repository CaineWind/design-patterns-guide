# 命令模式 (Command)

## 简介

命令模式是一种行为型设计模式，它将请求封装为独立的对象，从而可以用不同的请求对客户端进行参数化，支持请求的排队、记录日志以及撤销操作。

## 解决的问题

在以下场景中，直接调用方法存在局限性：

- 需要将操作放入队列中延迟执行
- 需要支持撤销/重做功能
- 需要记录操作日志以便回放
- 发送者和接收者需要完全解耦

命令模式将"做什么"和"谁来做"分离，让操作成为可存储、可传递、可组合的一等对象。

## 结构

| 角色 | 职责 |
|------|------|
| **Command（命令接口）** | 声明执行操作的 `execute()` 方法 |
| **ConcreteCommand（具体命令）** | 绑定接收者与动作，实现 `execute()` |
| **Receiver（接收者）** | 实际执行业务逻辑的对象 |
| **Invoker（调用者）** | 持有命令对象并触发执行 |

## 代码实现

```javascript
class Command {
  execute() {}
}

class Light {
  turnOn() {
    console.log("Light is on");
  }

  turnOff() {
    console.log("Light is off");
  }
}

class LightOnCommand extends Command {
  constructor(light) {
    super();
    this.light = light;
  }

  execute() {
    this.light.turnOn();
  }
}

class LightOffCommand extends Command {
  constructor(light) {
    super();
    this.light = light;
  }

  execute() {
    this.light.turnOff();
  }
}

class RemoteControl {
  constructor() {
    this.commands = [];
  }

  setCommand(command) {
    this.commands.push(command);
  }

  executeCommands() {
    this.commands.forEach(command => command.execute());
  }
}

// 使用方式
const light = new Light();
const lightOn = new LightOnCommand(light);
const lightOff = new LightOffCommand(light);

const remote = new RemoteControl();
remote.setCommand(lightOn);
remote.setCommand(lightOff);

remote.executeCommands();
```

**要点说明：**

- `Light` 是接收者，包含实际的业务操作（开灯、关灯）。
- `LightOnCommand` 和 `LightOffCommand` 是具体命令，各自封装了一个操作。
- `RemoteControl` 是调用者，它不知道命令的具体内容，只负责存储和触发。
- 命令可以被排队、组合执行，也可以轻松扩展为支持 `undo()` 方法。

## 运行结果

```
Light is on
Light is off
```

## 适用场景

- 编辑器的撤销/重做系统
- 游戏中的输入映射（按键 → 命令对象 → 角色动作）
- 任务队列和批处理系统
- GUI 按钮与菜单项的行为绑定
- 事务系统（一组操作要么全部成功，要么全部撤销）
