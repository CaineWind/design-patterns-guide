# Web 经典架构

[返回项目总览](../../README.md)

本章整理经典 Web 应用运行架构，以及浏览器 3D、科学可视化、医学影像和 AI Agent Web 应用的代表性技术架构。

> **名称说明：** 用户提供的 `deepseek-harss` 按官方项目名称归档为 **DeepSeek Harness (`dsh`)**。它属于插件化 AI Agent 应用，不是 3D 渲染库。

## 目录

- [经典 Web 运行架构](#经典-web-运行架构)
- [前端框架架构对比](#前端框架架构对比)
- [React 架构](#react-架构)
- [Vue 架构](#vue-架构)
- [Svelte 架构](#svelte-架构)
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

## 前端框架架构对比

React、Vue 和 Svelte 都使用组件组合 UI，但更新机制、编译器职责和全栈边界不同。

| 维度 | React | Vue | Svelte |
|------|-------|-----|--------|
| 定位 | UI 库和组件架构 | 渐进式前端框架 | 编译器优先的 UI 框架 |
| 组件格式 | JSX/TSX 函数组件 | Single-File Component 或渲染函数 | `.svelte` 单文件组件 |
| 核心响应方式 | 状态更新触发组件重新执行 | Proxy/ref 依赖跟踪触发渲染 Effect | Runes 和编译结果驱动细粒度更新 |
| 渲染模型 | Fiber Reconciler 比较元素树，Renderer 提交宿主变更 | 编译优化的 Virtual DOM Patch | 编译为直接 DOM 创建和更新代码 |
| 数据流 | Props 向下，事件和状态更新向上 | Props 向下、Events 向上，可选双向 `v-model` | Props、回调和可绑定状态，Runes 显式表达依赖 |
| 逻辑复用 | Hooks、Context、自定义 Hook | Composition API、Composable、Provide/Inject | Runes、`.svelte.js/.ts` 模块、Context、Store |
| 官方全栈边界 | 依赖 Next.js、React Router 等框架实现 | 依赖 Nuxt 等框架实现 | SvelteKit 提供官方应用框架 |
| 服务端能力 | SSR、Streaming、Server Components/Functions | SSR、Streaming/Hydration，常由 Nuxt 编排 | SvelteKit SSR、Prerender、SPA、Server Load/Actions |
| 主要优势 | 生态大、Renderer 可移植、复杂交互能力强 | 渐进采用、模板易读、响应式直接 | 编译优化、运行时较轻、细粒度 DOM 更新 |
| 主要代价 | 状态边界、Effect 和性能优化需要纪律 | Proxy/ref 语义、模板编译和响应式边界 | 编译语义、生态规模和 Svelte 版本迁移 |

## React 架构

React 将 UI 表达为组件树。组件根据 Props 和 State 返回元素描述，Reconciler 计算变化，具体 Renderer 把变化提交到 DOM、Native 或其他宿主环境。

### 核心分层

| 层次 | 责任 |
|------|------|
| Component / JSX | 用纯函数和 JSX 描述给定状态下的 UI |
| React Core | 定义元素、组件、Context、Hooks 和 Suspense 等公共模型 |
| Fiber Reconciler | 保存可中断的工作单元，协调优先级、重新渲染和树差异 |
| Scheduler | 调度不同优先级的渲染工作，支持并发响应能力 |
| Renderer | 将统一协调结果提交到具体宿主；Web 使用 React DOM |
| Host Environment | DOM、Native View 或自定义渲染目标 |
| Framework Layer | 路由、数据获取、构建、SSR、RSC 和部署由应用框架整合 |

### 状态与数据流

| 机制 | 责任 | 注意点 |
|------|------|--------|
| Props | 父组件向子组件传递只读输入 | 避免把可计算值重复保存为 State |
| Local State | 保存组件需要记忆的最小可变状态 | 状态由明确组件拥有，必要时向上提升 |
| Context | 向子树提供跨层共享值 | 高频变化会扩大订阅范围 |
| Reducer | 把复杂事件集中转换为新状态 | 适合状态机式交互和可测试更新 |
| Hooks | 在函数组件中组合状态、生命周期和外部系统同步逻辑 | 必须遵守调用顺序和纯渲染规则 |
| External Store | 把跨页面或服务端缓存状态放入独立 Store | 区分客户端 UI 状态与服务端数据缓存 |

### 客户端执行流

`用户事件 → setState/dispatch → 调度更新 → 执行受影响组件 → Fiber Reconcile → Commit DOM → Effect`

React 的单向数据流不是“所有状态放在一个 Store”，而是每份状态有明确所有者：状态和 Props 向下流动，事件通过回调触发上层更新。

### Server Components 架构

| 组件类型 | 运行位置 | 能力与限制 |
|----------|----------|------------|
| Server Component | 构建时或服务端 | 可直接访问服务端数据，不进入客户端 Bundle，不能使用交互 State |
| Client Component | 浏览器，也可参与服务端预渲染 | 使用 State、Effect 和浏览器 API，通过 `"use client"` 建立边界 |
| Server Function | 服务端 | 由客户端调用的服务端函数，以 `"use server"` 标记并序列化参数 |
| Suspense Boundary | 服务端与客户端 | 协调异步渲染、Streaming 和渐进展示 |

典型全栈流：

`Request → Server Component 读取数据 → RSC Payload + SSR HTML → Streaming → Client Component Hydration → 交互更新`

React 本身不规定目录、路由和数据层；大型应用通常使用 Feature-First 目录，并由 Next.js 或 React Router 等框架定义服务端与客户端边界。

## Vue 架构

Vue 采用“编译器 + 运行时响应式 + Virtual DOM”的混合架构。模板编译器把静态信息和动态绑定编码进 Render Function，运行时响应系统精确确定哪些组件需要重新执行，再由带编译提示的 Virtual DOM 完成 Patch。

### 核心分层

| 层次 | 责任 |
|------|------|
| Single-File Component | 在 `.vue` 文件中组织 Template、Script 和 Scoped Style |
| Compiler | 把 Template 编译为 Render Function，标记静态节点、Patch Flag 和更新块 |
| Reactivity | 通过 `reactive` Proxy、`ref` getter/setter、Effect、Computed 和 Watch 跟踪依赖 |
| Runtime Core | 管理组件实例、生命周期、VNode、调度器和跨平台渲染协议 |
| Runtime DOM | 把 Runtime Core 的宿主操作映射到浏览器 DOM 和事件系统 |
| Ecosystem | Vue Router 负责路由，Pinia 负责应用 Store，Nuxt 负责全栈和 SSR |

### 响应式模型

| 原语 | 作用 |
|------|------|
| `reactive()` | 用 Proxy 创建对象级深层响应状态 |
| `ref()` | 用带 `.value` 的容器保存原始值或对象引用 |
| `computed()` | 缓存由响应依赖派生的只读或可写值 |
| `watch()` | 显式监听来源，在变化时执行副作用 |
| `watchEffect()` | 自动收集同步执行期间读取的依赖 |
| Component Render Effect | 收集模板或 Render Function 读取的响应依赖并驱动组件更新 |

### 渲染执行流

`SFC Template → Compiler → Render Function → VNode/Block Tree → Renderer → DOM`

更新流程：

`用户事件 → 修改 reactive/ref → Proxy trigger → Scheduler 批处理 → Component Render Effect → VNode Patch → DOM`

Vue 的响应式依赖主要在运行时跟踪，但模板编译器会生成 Patch Flag、静态提升和 Block Tree，减少 Virtual DOM 更新时需要遍历的范围。

### 应用组织

| 规模 | 推荐结构 |
|------|----------|
| 渐进增强 | 在现有 HTML 页面挂载局部 Vue Application |
| 中型 SPA | SFC + Composition API + Vue Router + Pinia + Feature 模块 |
| 大型应用 | Feature-First + Composable + Service/Repository + Pinia Store |
| 全栈 / SSR | Nuxt 负责文件路由、服务端数据、SSR、Hydration 和部署适配 |

Options API 和 Composition API 是两种组件逻辑组织方式，底层共享同一响应式和渲染系统。复杂业务更适合用 Composition API 按功能关注点提取 Composable。

## Svelte 架构

Svelte 把大量框架工作放在构建阶段。编译器分析 `.svelte` 组件和 Runes，将声明式模板转换为直接创建、更新和销毁 DOM 的 JavaScript；运行时只保留响应式图、生命周期和必要的共享能力。

### 核心分层

| 层次 | 责任 |
|------|------|
| `.svelte` Component | 组合 Script、Template 和 Scoped Style |
| Svelte Compiler | 分析模板与 Runes，生成 DOM 操作、CSS 和服务端渲染代码 |
| Runes Reactivity | 用 `$state`、`$derived`、`$effect`、`$props` 显式表达响应关系 |
| Generated Component | 执行编译生成的细粒度 DOM 创建和更新逻辑 |
| Runtime | 提供 Effect Graph、Context、Lifecycle、Store 兼容和 Hydration 支持 |
| SvelteKit | 提供文件路由、Load、Form Actions、Server Routes、SSR 和部署 Adapter |

### Runes 响应模型

| Rune | 责任 |
|------|------|
| `$state` | 创建响应状态；对象和数组可形成深层 Proxy |
| `$derived` | 声明从其他状态计算出的派生值 |
| `$effect` | 在浏览器中响应依赖变化执行副作用 |
| `$props` | 声明组件从父级接收的输入 |
| `$bindable` | 明确允许父子间绑定的 Prop |
| `$inspect` | 在开发阶段观察响应状态变化 |

### 编译与更新流

构建流程：

`.svelte Source → Parser/Compiler → Dependency Analysis → Generated JS/CSS → Bundle`

运行流程：

`用户事件 → 更新 $state → 标记依赖节点 → 更新对应 DOM 表达式 → 运行相关 $effect`

Svelte 不依赖通用 Virtual DOM Diff；编译器已知道哪些 DOM 节点依赖哪些值，因此可以生成更直接的更新代码。

### SvelteKit 全栈架构

| 组成 | 责任 |
|------|------|
| `+page.svelte` / `+layout.svelte` | 页面和嵌套布局 UI |
| Universal `load` | 可在服务端和浏览器运行的数据加载 |
| `+page.server` / `+layout.server` | 仅服务端的数据访问和 Form Actions |
| `+server` | HTTP API Endpoint |
| Hooks | 请求、认证和错误处理等横切逻辑 |
| Page Options | 按路由选择 SSR、CSR、Prerender 策略 |
| Adapter | 将构建产物部署到 Node、Serverless、Edge 或 Static Host |

典型请求流：

`Request → Server Hook → Route Match → Server/Universal Load → SSR HTML → Hydration → Client Navigation`

SvelteKit 允许同一应用按页面混合 SSR、静态预渲染和客户端渲染，但服务端模块与浏览器模块必须保持明确边界，防止密钥和仅服务端依赖进入客户端 Bundle。

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
| 最大生态和跨端组件能力 | React | 组件与 Renderer 架构成熟，全栈框架和第三方生态丰富 |
| 渐进接入和模板驱动业务应用 | Vue | 可局部挂载，响应式直接，官方 Router/Store 组合清晰 |
| 编译器优先和较轻运行时 | Svelte | 生成细粒度 DOM 更新，SvelteKit 提供完整应用骨架 |
| 自由构建通用 3D 交互 | Three.js | 抽象精简，应用结构自主度高 |
| Web 游戏、XR、完整引擎能力 | Babylon.js | 内置渲染、物理、GUI、音频和工具链 |
| 临床 DICOM 查看和标注 | Cornerstone3D | 医学影像 Loader、Viewport、Metadata 和 Tools 完整 |
| 科学数据、网格和体渲染 | VTK.js | Pipeline、DataModel、Filter 和 Volume Rendering 成熟 |
| 可扩展 IDE 或工具工作台 | VS Code 架构 | 分层核心、Extension Host、LSP/DAP 和远程工作区成熟 |
| 可组合 AI Agent Web 工作台 | DeepSeek Harness | 微内核、插件化 Provider/Tool 和多宿主 |

### 典型组合

| 场景 | 推荐组合 |
|------|----------|
| 大型 React 产品 | Feature-First + React Framework + Server Components/SSR + Query Cache + Design System |
| 企业 Vue 应用 | SFC + Composition API + Vue Router + Pinia + Feature 模块 |
| Svelte 全栈应用 | SvelteKit + Runes + Server Load/Actions + 按路由 SSR/Prerender |
| 数字孪生平台 | React/Vue Shell + Three.js/Babylon.js + Worker + BFF + 流式资源 |
| 医学诊断工作站 | Cornerstone3D + DICOMweb + UDF Store + Worker 解码 |
| 医学科研可视化 | Cornerstone3D 管理临床影像 + VTK.js 处理网格/体数据 |
| 科学可视化门户 | VTK.js + WASM Filter + Remote Rendering + SSR 应用壳 |
| 云 IDE / 低代码工作台 | VS Code 分层核心 + Monaco + Extension Host + LSP/DAP + Remote Server |
| AI 辅助可视化 | Three.js/VTK.js/Cornerstone3D 工具层 + Agent 服务 + 受限 Tool Plugin |
| 本地 Agent 工作台 | DeepSeek Harness Web UI + 固定 Provider + 最小权限插件集 |

## 参考资料

- [React: Thinking in React](https://react.dev/learn/thinking-in-react)
- [React Server Components](https://react.dev/reference/rsc/server-components)
- [React Reference Architecture](https://react.dev/reference/react)
- [Vue Reactivity in Depth](https://vuejs.org/guide/extras/reactivity-in-depth.html)
- [Vue Rendering Mechanism](https://vuejs.org/guide/extras/rendering-mechanism.html)
- [Vue Server-Side Rendering](https://vuejs.org/guide/scaling-up/ssr.html)
- [Svelte Overview](https://svelte.dev/docs/svelte/overview)
- [Svelte Runes](https://svelte.dev/docs/svelte/what-are-runes)
- [SvelteKit Introduction](https://svelte.dev/docs/kit/introduction)
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
