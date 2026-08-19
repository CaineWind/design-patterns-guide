# 应用端架构

[返回项目总览](../../README.md)

本章覆盖 Web、移动端、桌面端和嵌入式应用，重点整理 UI 展示模式、状态流、内部模块边界以及跨平台运行方式。

## 目录

- [UI 展示与状态模式](#ui-展示与状态模式)
- [应用内部模块组织](#应用内部模块组织)
- [Web、移动与桌面运行架构](#web移动与桌面运行架构)
- [主流技术架构映射](#主流技术架构映射)
- [常见组合](#常见组合)

## UI 展示与状态模式

| 架构 | 数据与控制关系 | 适合 | 代表生态 |
|------|----------------|------|----------|
| MVC | Controller 处理输入并协调 Model 与 View | 传统桌面和服务端页面 | UIKit、ASP.NET MVC、Rails |
| MVP | Presenter 持有展示逻辑，通过 View 接口更新界面 | 被动 View、强调单测的传统 UI | Android 早期应用、桌面 GUI |
| MVVM | View 绑定 ViewModel 暴露的状态和命令 | 数据绑定、声明式 UI | WPF、Android、SwiftUI、Vue |
| MVI | Intent 触发 Reducer 生成不可变 State | 状态复杂、要求可追踪 | Android MVI、Redux 风格应用 |
| MVU / Elm Architecture | Model、View、Update 构成纯函数循环 | 函数式和声明式 UI | Elm、Fabulous |
| Flux | 单向 Dispatcher、Store、View 数据流 | 大型前端状态管理 | Flux、React 生态 |
| Redux | Action 经过 Reducer 更新单一 Store | 可预测状态、调试和回放 | Redux Toolkit、NgRx |
| VIPER | View、Interactor、Presenter、Entity、Router 分工 | 大型 iOS 团队和页面模块 | iOS |
| RIBs | Router、Interactor、Builder 组成业务树 | 大型跨平台移动应用 | Uber RIBs |
| Clean Swift / VIP | View、Interactor、Presenter 单向传递场景数据 | iOS 用例隔离 | Clean Swift |
| PAC | Presentation、Abstraction、Control 组成层次代理 | 复杂交互式系统 | GUI 系统 |
| Component-Based UI | 页面由封装状态、行为和视图的小组件组合 | Web、声明式移动端 | React、Vue、Angular、Flutter |
| Unidirectional Data Flow | 状态向下、事件向上，状态持有者统一更新 | Compose、React、复杂 UI | Android 推荐架构、React |
| State Machine / Statechart | 显式状态、事件、守卫和转换驱动 UI | 登录、支付、多步骤流程 | XState、StateFlow |
| Presentation Model | 独立模型表示 View 状态和行为 | 多 View 复用、自动化测试 | 桌面和企业 UI |

## 应用内部模块组织

| 架构 | 核心思想 | 适合 | 主要代价 |
|------|----------|------|----------|
| Layer-First | 按 UI、Domain、Data 等技术层组织 | 小中型、按技术分工 | 一个功能散落在多个目录 |
| Feature-First | 每个功能包含自己的 UI、状态、数据和测试 | 产品迭代、功能团队 | 跨功能共享边界需治理 |
| Clean Architecture | 业务规则位于内层，平台和框架位于外层 | 多平台、长期维护、强测试 | 映射和抽象代码增加 |
| Hexagonal App | UI、网络、数据库是领域端口的适配器 | 核心业务跨端复用 | 对简单应用可能过重 |
| Modular App | 以编译模块隔离功能和依赖 | 大型移动端、超级应用 | 构建、导航和模块通信复杂 |
| Plugin / Extension | Shell 提供扩展点，功能动态或静态插入 | IDE、浏览器、桌面工具 | 安全、权限和版本兼容 |
| Micro Frontends | 前端按业务域独立开发、发布并在 Shell 组合 | 多团队大型 Web | 体验一致性、依赖重复 |
| Super App / Mini Program | 宿主提供运行时，子应用按沙箱协议加载 | 平台型移动应用 | 审核、沙箱和包管理 |
| Offline-First | 本地数据源优先，后台与远端同步 | 移动、弱网、现场作业 | 冲突合并和同步状态 |
| Local-First | 设备持有主要数据，多设备通过协作协议合并 | 笔记、协同编辑、隐私工具 | CRDT、权限和备份复杂 |
| Repository + Data Source | Repository 统一本地、网络和缓存数据来源 | 移动端、桌面业务应用 | 缓存和事实来源设计 |
| Shared Core + Platform Adapters | 共享领域层，各平台保留原生 UI 和能力适配 | Kotlin Multiplatform、共享业务 SDK | 边界选择和跨语言调用 |

## Web、移动与桌面运行架构

| 架构 | 核心思想 | 优点 | 主要代价 / 代表实现 |
|------|----------|------|---------------------|
| Native App | 使用平台原生语言、UI 和系统 API | 体验、性能、平台能力最佳 | 多平台重复开发 |
| Cross-Compiled UI | 一套代码编译到多平台并自绘或映射控件 | 高复用、一致 UI | 平台适配和包体；Flutter、.NET MAUI |
| Shared Logic + Native UI | 共享领域和数据逻辑，各端实现原生界面 | 复用 + 原生体验 | UI 仍需多套；Kotlin Multiplatform |
| Hybrid / WebView | 原生壳承载 Web UI，插件访问设备能力 | Web 团队上手快 | WebView 性能和一致性；Ionic |
| JavaScript Native Bridge | JavaScript 驱动原生组件 | 高复用、接近原生控件 | Bridge 和运行时开销；React Native |
| SPA | 首次加载应用壳，客户端路由和渲染页面 | 交互流畅、前后端分离 | 首屏、SEO、包体 |
| MPA | 每次导航由服务器返回新页面 | 简单、渐进增强、天然 SEO | 状态连续性和页面切换 |
| SSR | 服务端按请求生成 HTML，客户端 Hydration | 首屏和 SEO | 服务端成本和双端环境 |
| SSG | 构建时生成静态 HTML | 快、便宜、安全 | 内容更新需重新构建 |
| ISR | 静态页面按时间或请求增量再生成 | 兼顾新鲜度和性能 | 缓存一致性和平台依赖 |
| Islands Architecture | 默认静态，仅交互岛加载 JavaScript | 少 JavaScript、内容站性能好 | 跨岛共享状态；Astro |
| Partial Hydration | 只对需要的区域按优先级 Hydrate | 改善首屏和交互时间 | 序列化和组件边界 |
| Resumability | 序列化服务端上下文，客户端从事件处恢复 | 避免全量 Hydration | 工具链和约束；Qwik |
| Progressive Web App | Service Worker、Manifest 和缓存提供安装与离线 | 单 URL、跨平台 | 系统 API 受平台限制 |
| Electron | Chromium + Node.js 打包 Web 桌面应用 | Web 生态完整 | 内存和包体较大 |
| Tauri / Native WebView Shell | 系统 WebView + 原生/Rust 后端 | 包体和资源占用较低 | WebView 差异和插件能力 |
| Embedded / IoT App | 事件循环或 RTOS 任务运行 UI 和设备逻辑 | 资源可控、实时性 | 内存、功耗和硬件约束 |

## 主流技术架构映射

| 技术 | 主要架构特征 |
|------|--------------|
| React + Redux | Component UI、单向数据流、Store/Reducer、Hooks |
| Vue + Pinia | 组件、响应式数据、组合式 API、集中 Store |
| Angular + NgRx | 依赖注入、组件、Observable、Redux 风格状态 |
| Flutter | Widget Tree、声明式 UI、响应式状态、跨平台自绘 |
| Android Jetpack Compose | 声明式 UI、ViewModel、UDF、Repository、可选 Domain Layer |
| UIKit | MVC，也常组合 MVP、MVVM、VIPER、Coordinator |
| SwiftUI | 声明式 View、单一事实来源、状态/绑定、观察式数据流 |
| .NET MAUI / WPF | XAML、Data Binding、MVVM |
| Next.js / Nuxt / SvelteKit | 文件路由、SSR/SSG/混合渲染、客户端与服务端边界 |
| Electron / Tauri | Web UI + 桌面 Shell + 原生能力桥接 |

## 常见组合

| 场景 | 推荐组合 |
|------|----------|
| 普通移动应用 | Feature-First + MVVM/UDF + Repository |
| 弱网业务应用 | Feature-First + Offline-First + Repository + 后台同步 |
| 大型 Web 应用 | Component UI + UDF + Feature-First + SSR |
| 多团队前端平台 | Micro Frontends + Shell + BFF + 统一设计系统 |
| 跨平台产品 | Shared Core + Platform Adapter，或 Cross-Compiled UI + MVVM |
| 复杂交互流程 | UDF + State Machine + 不可变状态 |

## 参考资料

- [Android Guide to App Architecture](https://developer.android.com/topic/architecture)
- [React: Thinking in React](https://react.dev/learn/thinking-in-react)
- [Redux Fundamentals](https://redux.js.org/tutorials/fundamentals/part-1-overview)
- [Flutter App Architecture Guide](https://docs.flutter.dev/app-architecture)
