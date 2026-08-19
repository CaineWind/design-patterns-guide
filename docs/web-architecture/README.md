# Web 经典架构

[返回项目总览](../../README.md)

本章整理经典 Web 应用运行架构，以及浏览器 3D、科学可视化、医学影像和 AI Agent Web 应用的代表性技术架构。

> **名称说明：** 用户提供的 `deepseek-harss` 按官方项目名称归档为 **DeepSeek Harness (`dsh`)**。它属于插件化 AI Agent 应用，不是 3D 渲染库。

## 目录

- [经典 Web 运行架构](#经典-web-运行架构)
- [Web 可视化通用分层](#web-可视化通用分层)
- [代表技术对比](#代表技术对比)
- [Three.js 架构](#threejs-架构)
- [Babylon.js 架构](#babylonjs-架构)
- [Cornerstone3D 架构](#cornerstone3d-架构)
- [VTK.js 架构](#vtkjs-架构)
- [VS Code 架构](#vs-code-架构)
- [DeepSeek Harness 架构](#deepseek-harness-架构)
- [选型与组合](#选型与组合)

## 经典 Web 运行架构

| 架构 | 核心结构 | 适合 | 主要代价 |
|------|----------|------|----------|
| MPA | 每次导航由服务器返回完整页面 | 内容站、管理后台、渐进增强 | 页面切换和客户端状态连续性 |
| SPA | 应用壳 + 客户端路由 + API | 高交互工具、可视化工作台 | 首屏、SEO、包体和状态管理 |
| SSR + Hydration | 服务端输出 HTML，客户端接管交互 | 首屏和 SEO 都重要的应用 | 双端运行环境和 Hydration 成本 |
| SSG / ISR | 构建时生成页面，按需增量更新 | 文档、博客、产品内容 | 动态内容和缓存一致性 |
| Islands | 默认静态，仅交互岛加载脚本 | 内容为主、局部交互 | 跨岛共享状态 |
| PWA | Service Worker + Manifest + 离线缓存 | 可安装、弱网和离线 Web 应用 | 平台能力和缓存升级治理 |
| Micro Frontends | 多个业务前端在 Shell 中组合 | 多团队独立开发和发布 | 依赖重复、体验和路由治理 |
| BFF | 每类前端拥有专用后端聚合层 | 多端数据需求差异明显 | 后端数量和逻辑重复 |
| Web Worker Pipeline | 把解析、计算或解码放入 Worker | 大数据、影像、AI、复杂几何 | 数据复制、调度和错误传播 |
| WebAssembly Core | JavaScript 管理 UI，WASM 运行计算核心 | 编解码、科学计算、模型推理 | ABI、内存交换和调试 |
| Plugin-Driven Web App | Shell 提供事件、服务和扩展点，功能由插件装配 | IDE、Agent、低代码和平台型应用 | 插件安全、生命周期和版本兼容 |

## Web 可视化通用分层

| 层次 | 责任 | 常见对象 |
|------|------|----------|
| 应用 Shell | 路由、布局、权限、工作区和生命周期 | React/Vue Shell、Web Components |
| 业务状态 | 保存选择、工具状态、布局和会话状态 | Store、State Machine、Context |
| 数据接入 | 请求远端数据、解析格式、提供元数据 | Loader、Reader、DICOMweb、glTF |
| 缓存与调度 | 控制内存、并发、优先级和取消 | Cache、Request Pool、Worker Pool |
| 场景或数据模型 | 表达对象层次、几何、体数据和属性 | Scene Graph、ImageData、PolyData、Volume |
| 处理管线 | 对原始数据进行过滤、重采样、分割或转换 | Filter、Algorithm、Processor |
| 映射与渲染 | 把数据映射为图元、材质和最终像素 | Mapper、Material、Actor、Renderer |
| 交互工具 | 拾取、相机、测量、标注和同步 | Controls、ToolGroup、Widget、Interactor |
| GPU / 计算后端 | 执行 WebGL、WebGPU、WASM 或远程渲染 | Render Backend、Shader、Remote View |

## 代表技术对比

| 技术 | 类型 | 核心架构 | 优先选择场景 |
|------|------|----------|--------------|
| Three.js | 通用 3D 渲染库 | Object3D 场景图 + Scene + Camera + Renderer | 自定义 3D 产品、数字孪生、交互展示 |
| Babylon.js | 完整 Web 3D / 游戏引擎 | Engine + Scene + Node 系统 + 完整子系统 | Web 游戏、XR、需要完整引擎能力的 3D 应用 |
| Cornerstone3D | 医学影像框架 | Loader/Metadata + Cache + RenderingEngine/Viewport + Tools | DICOM、放射影像、诊断工作站 |
| VTK.js | 科学可视化库 | Data Source/Reader + Filter + Mapper + Actor/Volume + Renderer | 科学数据、体渲染、网格和医学科研 |
| VS Code | 平台型 Web/桌面工作台 | 分层核心 + Workbench + Monaco + Extension Host + 协议进程 | IDE、低代码、开发者工具和可扩展工作台 |
| DeepSeek Harness | AI Agent 应用框架 | Cordis 驱动的微内核 + 一切皆插件 + Web UI | 可组合 Agent、工具调用、模型 Provider 和本地 Web 工作台 |

## Three.js 架构

Three.js 是偏底层、非强约束的渲染库。它提供场景图和渲染抽象，但应用状态、业务实体、资源生命周期和大型项目分层通常由开发者自行设计。

### 核心对象

| 对象 | 责任 |
|------|------|
| `Object3D` | 场景图基类，提供父子关系、变换和生命周期回调 |
| `Scene` | 场景图根节点，保存环境、雾和可渲染对象 |
| `Camera` | 定义观察投影和视锥 |
| `Geometry / BufferGeometry` | 保存顶点、索引和顶点属性 |
| `Material` | 描述着色、纹理、混合和渲染状态 |
| `Mesh / Line / Points` | 组合几何与材质形成可渲染对象 |
| `Renderer` | 遍历场景，以 Camera 视角向 Canvas 输出图像 |
| `Loader` | 加载 glTF、纹理、模型和环境资源 |
| `Raycaster / Controls` | 提供拾取和相机交互 |
| `EffectComposer` | 以 Pass Pipeline 组织后处理 |

### 典型流程

`Loader → Geometry/Texture → Material → Mesh → Scene Graph → Camera → Renderer → Canvas`

### 工程化建议

| 规模 | 推荐组织 |
|------|----------|
| 小型展示 | Scene + Camera + Renderer + 单一动画循环 |
| 中型交互应用 | 业务实体与 Object3D 分离，增加 AssetManager、Input、Store 和 Disposal |
| 大型数字孪生 | Feature 模块 + ECS/领域模型 + Worker 数据管线 + LOD/分块流式加载 |
| React 项目 | 可选 React Three Fiber，用组件树声明 Three.js 场景图 |

## Babylon.js 架构

Babylon.js 比 Three.js 更接近完整引擎：除场景图和渲染外，还整合资源、动画、物理、音频、GUI、XR、行为和调试工具。

### 核心分层

| 层次 | 责任 |
|------|------|
| `Engine` | Canvas、WebGL/WebGPU 后端、渲染循环和底层资源 |
| `Scene` | 场景生命周期、节点集合、动画、物理、音频和渲染阶段 |
| Node 系统 | TransformNode、Mesh、Camera、Light 等场景图节点 |
| Material / Geometry | PBR、Shader、纹理、顶点与实例数据 |
| Observable | 用观察者机制暴露渲染、输入和资源生命周期事件 |
| AssetContainer / Loader | 加载、暂存、实例化和卸载场景资源 |
| Frame Graph / Node Render Graph | 声明渲染 Pass、资源依赖和高级渲染管线 |
| 子系统 | GUI、Particles、Physics、Animation、XR、Inspector |

### 典型流程

`Engine → Scene → Camera/Light/Mesh → Material → Render Loop → Frame Graph/Renderer → Canvas`

### 与 Three.js 的架构差异

| 维度 | Three.js | Babylon.js |
|------|----------|------------|
| 定位 | 可组合渲染库 | 完整 3D / 游戏引擎 |
| 应用骨架 | 开发者自行搭建 | Engine/Scene 生命周期较完整 |
| 内置子系统 | 精简，常依赖生态扩展 | GUI、物理、音频、XR、Inspector 较齐全 |
| 控制程度 | 更自由、更接近渲染层 | 约定更多、开箱能力更强 |

## Cornerstone3D 架构

Cornerstone3D 面向医学影像，不只是通用渲染器。它把影像标识、元数据、DICOM 加载、缓存、视口、工具和标注状态拆为多个可替换包。

### 包与职责

| 模块 | 责任 |
|------|------|
| `@cornerstonejs/core` | RenderingEngine、Viewport、Cache、Image/Volume Loader 和 Metadata Provider |
| `@cornerstonejs/tools` | 测量、标注、分割、ToolGroup、Synchronizer 和交互状态 |
| `@cornerstonejs/dicom-image-loader` | DICOM P10、WADO-URI、WADO-RS、解码和 Worker |
| Adapters | 在 Cornerstone 状态与 DICOM SR、Segmentation 等标准间转换 |
| AI / Segmentation 扩展 | AI 结果、Labelmap、Contour 和 Surface 表示 |

### 核心机制

| 机制 | 说明 |
|------|------|
| Scheme Loader | `imageId` 或 `volumeId` 的协议前缀决定使用哪个 Loader |
| Metadata Provider | 按优先级提供空间、序列、显示和设备元数据 |
| Cache | 管理影像与体数据内存，并处理淘汰 |
| RenderingEngine | 管理一个或多个 Viewport 及共享 GPU 资源 |
| Viewport | Stack、Volume、Video 等不同显示模型 |
| ToolGroup | 把一组工具及其模式绑定到 Viewport |
| Synchronizer | 在多个视口间同步相机、窗宽窗位或位置 |
| Annotation State | 标注使用物理空间坐标，可在多个 Viewport 中复用 |

### 典型数据流

`DICOMweb/File → ImageId → DICOM Image Loader/Worker → Metadata + Pixel Data → Cache → RenderingEngine/Viewport → ToolGroup/Annotation`

### 与 VTK.js 的边界

Cornerstone3D 优先解决临床影像查看、DICOM 语义和诊断工具；VTK.js 优先解决通用科学数据管线、网格、过滤算法和体可视化。复杂医学 Web 应用可以组合两者。

## VTK.js 架构

VTK.js 将 VTK 的数据模型和可视化 Pipeline 带到浏览器，核心特征是数据源、过滤器、映射器和渲染对象之间的显式管线。

### Pipeline

| 阶段 | 典型对象 | 责任 |
|------|----------|------|
| Source / Reader | ConeSource、XMLReader、OBJReader | 生成或读取数据 |
| Data Model | ImageData、PolyData、DataArray | 表达体素、网格和属性 |
| Filter / Algorithm | Clip、Contour、Reslice、Transform | 处理输入并产生新数据 |
| Mapper | Mapper、VolumeMapper、ImageMapper | 把数据映射为渲染图元 |
| Prop | Actor、Volume、ImageSlice | 组合 Mapper、属性和空间变换 |
| Renderer | Renderer、Camera、Light | 组织视图、相机和场景内容 |
| RenderWindow | OpenGL/WebGPU View、Interactor | 管理输出窗口、后端和交互 |

典型流程：

`Reader/Source → Filter → Mapper → Actor/Volume → Renderer → RenderWindow → Canvas`

### 模块结构

| 模块 | 责任 |
|------|------|
| Common | Core 数据数组、DataModel、数学和变换 |
| Filters | 数据处理、建模和数据源 |
| IO | 文件与网络数据读取 |
| Imaging | 重采样、切片和影像处理 |
| Rendering | Mapper、Actor、Volume、Renderer 和后端 |
| Interaction | 相机和输入交互样式 |
| Widgets | WidgetState、Representation、Behavior 和 WidgetManager |
| Proxy | 用更高层对象简化 Source、Representation 和 View 的装配 |

VTK.js Widget 使用 MVC 思路：WidgetState 是共享事实来源，Representation 负责显示，Behavior 负责交互；同一状态可以同步到多个 Renderer。

## VS Code 架构

VS Code 是基于 Electron 和浏览器技术构建的平台型工作台。它把编辑器内核、工作台 UI、平台服务和业务贡献分层，并让扩展、语言服务、调试器和远程工作区运行在隔离进程或不同机器上。

### 源码分层

| 层次 | 责任 | 依赖方向 |
|------|------|----------|
| `base` | 通用工具、生命周期、事件、异步原语和基础 UI 构件 | 最底层，不依赖编辑器或工作台 |
| `platform` | 依赖注入和文件、配置、日志、存储、键盘映射等共享服务 | 依赖 `base` |
| `editor` | Monaco Editor 的模型、视图、命令、语言特性接口 | 依赖 `base`、`platform` |
| `workbench` | 编辑器组、侧边栏、面板、状态栏、Notebook、自定义编辑器和应用 Shell | 依赖前三层 |
| `workbench/services` | 工作台核心共享服务 | 供 Workbench Contribution 使用 |
| `workbench/contrib` | 搜索、Git、调试、终端等具体功能贡献 | 通过注册点和服务接入核心 |
| Extension API | 向第三方扩展暴露稳定的 `vscode` 命名空间和 Contribution Points | 通过代理连接 Workbench 与 Extension Host |

### 多进程与运行位置

| 组件 | 运行位置 | 责任 |
|------|----------|------|
| Electron Main Process | 本地桌面主进程 | 应用生命周期、窗口、菜单、系统集成和进程管理 |
| Renderer / Workbench | Electron Renderer 或浏览器 | 渲染 Workbench UI、Monaco 和交互界面 |
| Local Extension Host | 本地 Node.js 进程 | 运行桌面扩展并隔离扩展故障和阻塞 |
| Web Extension Host | 浏览器 Web Worker | 在 `vscode.dev` 等 Web 环境运行浏览器兼容扩展 |
| Remote Extension Host | 远端 Node.js 进程 | 在 SSH、容器、WSL 或 Codespaces 的工作区侧运行扩展 |
| Language Server | 独立本地或远程进程 | 解析项目并通过 LSP 提供补全、诊断、跳转和重构 |
| Debug Adapter | 独立进程或扩展组件 | 通过 DAP 把统一调试 UI 适配到不同运行时 |
| VS Code Server | 远程工作区主机 | 承载远程扩展、文件、终端和工作区服务 |

### 扩展架构

| 机制 | 说明 |
|------|------|
| Extension Manifest | `package.json` 声明入口、能力、兼容版本和 Activation Events |
| Contribution Points | 声明命令、菜单、语言、主题、视图、调试器等静态贡献 |
| Activation Events | 只在对应语言、命令或工作区出现时延迟激活扩展 |
| Extension API | 通过 `commands`、`window`、`workspace`、`languages` 等命名空间访问能力 |
| Extension Host Isolation | 扩展不直接修改 Workbench DOM，通过 RPC/API 请求宿主操作 |
| Extension Kind | `ui` 或 `workspace` 表明扩展靠近用户界面还是工作区运行 |
| Web Extension | 使用 `browser` 入口并在 Web Worker 中运行，不能依赖完整 Node.js API |

### 协议驱动的能力隔离

| 协议 / 边界 | 数据流 | 架构价值 |
|-------------|--------|----------|
| Extension Host RPC | Workbench ↔ Extension Host | 隔离第三方代码，保持 UI 响应 |
| Language Server Protocol | Editor/Language Client ↔ Language Server | 语言工具与编辑器解耦，支持跨语言实现 |
| Debug Adapter Protocol | Debug UI ↔ Debug Adapter ↔ Runtime | 统一多语言、多运行时调试体验 |
| Remote Agent Protocol | Local/Web Client ↔ VS Code Server | UI 靠近用户，计算和文件能力靠近工作区 |

### 典型执行流

扩展激活：

`用户事件 → Workbench Contribution/Command → Activation Event → Extension Host → Extension API RPC → Workbench 更新`

语言服务：

`Monaco Text Model → Language Client → LSP → Language Server → Diagnostics/Completion → Monaco`

远程开发：

`Local VS Code 或 vscode.dev → Tunnel/Remote Connection → VS Code Server → Remote Extension Host → Remote File/Terminal/Language Server`

### 可复用的架构思想

| 思想 | VS Code 中的体现 | 适合复用到 |
|------|------------------|------------|
| 分层核心 | `base/platform/editor/workbench` 单向依赖 | 大型前端、桌面工作台 |
| 微内核 + 插件 | 最小 Workbench 核心 + Contribution + Extension API | IDE、低代码、设计工具 |
| 服务化内核 | 通过依赖注入访问文件、配置、日志和存储服务 | 可测试的大型 TypeScript 应用 |
| 多进程隔离 | UI、扩展、语言服务和调试器分进程 | 不可信插件和高计算任务 |
| 协议适配 | LSP、DAP 和远程协议统一异构实现 | 多语言、多后端平台 |
| 本地/远程对称 | UI 能力留在本地，工作区能力靠近代码运行 | 云 IDE、远程运维工具 |
| 延迟激活 | 按事件加载扩展 | 插件数量多、启动性能敏感的应用 |

## DeepSeek Harness 架构

DeepSeek Harness 是开源 Agent Harness，官方描述为“一切皆插件”。其核心不是传统页面 MVC，而是以 Cordis 为基础的微内核和插件组合架构，Web UI 是其中一个宿主应用。

### 核心分层

| 层次 | 责任 |
|------|------|
| Cordis Runtime | 提供上下文、服务、事件和插件生命周期 |
| Harness Core | 组织 Agent 会话、消息、模型调用和工具执行 |
| Plugin Contract | 允许 Provider、Tool、Memory、UI 和工作流能力被替换或组合 |
| Provider Plugins | 对接 DeepSeek、OpenAI、Anthropic 或兼容模型服务 |
| Tool / Skill Plugins | 提供文件、终端、搜索、浏览器和领域工具 |
| Session / Trajectory | 保存会话历史、工具调用和可审计执行轨迹 |
| Host Apps | CLI、Web UI 及其他运行入口 |
| Web UI | 本地工作台、交互界面和插件管理入口 |

### 典型执行流

`Web UI/CLI → Session → Agent Loop → Model Provider → Tool Plugin → Observation → Session/Trajectory → UI`

### 架构特征

| 特征 | 价值 | 风险 |
|------|------|------|
| 一切皆插件 | 模型、工具和 UI 可替换、可重组 | 插件权限、来源和版本治理 |
| 微内核 | 核心保持精简，功能在扩展层演进 | 服务契约和生命周期复杂 |
| 事件驱动 | 模块通过上下文和事件解耦 | 调用链追踪和错误传播 |
| 多宿主 | 同一能力可由 CLI 或 Web UI 使用 | 状态同步和环境差异 |
| 执行轨迹 | 支持审计、调试和复盘 | 敏感信息和存储生命周期 |

> DeepSeek Harness 当前官方标记为 Developer Preview，兼容性可能发生破坏性变化；生产采用前应固定版本并审查插件权限。

## 选型与组合

### 选择矩阵

| 需求 | 首选 | 原因 |
|------|------|------|
| 自由构建通用 3D 交互 | Three.js | 抽象精简，应用结构自主度高 |
| Web 游戏、XR、完整引擎能力 | Babylon.js | 内置渲染、物理、GUI、音频和工具链 |
| 临床 DICOM 查看和标注 | Cornerstone3D | 医学影像 Loader、Viewport、Metadata 和 Tools 完整 |
| 科学数据、网格和体渲染 | VTK.js | Pipeline、DataModel、Filter 和 Volume Rendering 成熟 |
| 可扩展 IDE 或工具工作台 | VS Code 架构 | 分层核心、Extension Host、LSP/DAP 和远程工作区成熟 |
| 可组合 AI Agent Web 工作台 | DeepSeek Harness | 微内核、插件化 Provider/Tool 和多宿主 |

### 典型组合

| 场景 | 推荐组合 |
|------|----------|
| 数字孪生平台 | React/Vue Shell + Three.js/Babylon.js + Worker + BFF + 流式资源 |
| 医学诊断工作站 | Cornerstone3D + DICOMweb + UDF Store + Worker 解码 |
| 医学科研可视化 | Cornerstone3D 管理临床影像 + VTK.js 处理网格/体数据 |
| 科学可视化门户 | VTK.js + WASM Filter + Remote Rendering + SSR 应用壳 |
| 云 IDE / 低代码工作台 | VS Code 分层核心 + Monaco + Extension Host + LSP/DAP + Remote Server |
| AI 辅助可视化 | Three.js/VTK.js/Cornerstone3D 工具层 + Agent 服务 + 受限 Tool Plugin |
| 本地 Agent 工作台 | DeepSeek Harness Web UI + 固定 Provider + 最小权限插件集 |

## 参考资料

- [Three.js Fundamentals](https://threejs.org/manual/en/fundamentals.html)
- [Three.js Object3D](https://threejs.org/docs/pages/Object3D.html)
- [Babylon.js Engine Specifications](https://www.babylonjs.com/specifications/)
- [Cornerstone3D Overview](https://www.cornerstonejs.org/docs/getting-started/overview/)
- [Cornerstone3D Project Scope](https://www.cornerstonejs.org/docs/getting-started/scope/)
- [VTK.js Overview](https://kitware.github.io/vtk-js/docs/)
- [VTK.js API](https://kitware.github.io/vtk-js/api/)
- [VS Code Source Code Organization](https://github.com/microsoft/vscode/wiki/Source-Code-Organization)
- [VS Code Extension Host](https://code.visualstudio.com/api/advanced-topics/extension-host)
- [VS Code Language Server Extension Guide](https://code.visualstudio.com/api/language-extensions/language-server-extension-guide)
- [VS Code Server](https://code.visualstudio.com/docs/remote/vscode-server)
- [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness)
