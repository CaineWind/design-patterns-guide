# 适配器模式 (Adapter)

## 简介

适配器模式是一种结构型设计模式，它将一个类的接口转换成客户端期望的另一个接口，使原本因接口不兼容而无法协作的类能够一起工作。

## 解决的问题

在实际开发中，经常遇到以下情况：

- 引入了新的第三方库，但其接口与现有代码不兼容
- 系统升级后新组件的 API 发生了变化，但旧的调用方无法修改
- 需要统一多个不同接口的组件，使其对外表现一致

适配器模式在不修改原有类的前提下，通过包装器（Wrapper）桥接新旧接口。

## 结构

| 角色 | 职责 |
|------|------|
| **Target（目标接口）** | 客户端所期望的接口 |
| **Adaptee（被适配者）** | 已有的、接口不兼容的类 |
| **Adapter（适配器）** | 将 Adaptee 的接口转换为 Target 接口 |

## 代码实现

```javascript
class OldAudioPlayer {
  playFile(filename) {
    return `Playing OLD audio file: ${filename}`;
  }
}

class NewAudioPlayer {
  play(filename, format) {
    return `Playing NEW ${format} file: ${filename}`;
  }
}

class AudioPlayerAdapter {
  constructor() {
    this.newAudioPlayer = new NewAudioPlayer();
  }

  playFile(filename) {
    const format = filename.split('.').pop();
    return this.newAudioPlayer.play(filename, format);
  }
}

// 使用方式
const oldPlayer = new OldAudioPlayer();
console.log(oldPlayer.playFile("song.mp3"));

const adaptedPlayer = new AudioPlayerAdapter();
console.log(adaptedPlayer.playFile("song.mp3"));
```

**要点说明：**

- `OldAudioPlayer` 是旧接口，使用 `playFile(filename)` 方法。
- `NewAudioPlayer` 是新实现，使用 `play(filename, format)` 签名，接口不兼容。
- `AudioPlayerAdapter` 实现了旧接口 `playFile()`，内部将调用委托给 `NewAudioPlayer`，并自动从文件名中提取格式参数。
- 客户端代码无需修改，仍然调用 `playFile()` 即可使用新播放器的能力。

## 运行结果

```
Playing OLD audio file: song.mp3
Playing NEW mp3 file: song.mp3
```

## 适用场景

- 对接第三方 SDK 时统一调用接口
- 数据库迁移时兼容旧的查询 API
- 前端组件升级时保持对外 props 接口不变
- 集成遗留系统与现代系统
