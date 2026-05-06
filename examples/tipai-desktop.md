# 示例: TipAi Desktop 方案全流程

## 用户输入
> "我想做一个本地优先的 AI 提示词工程桌面工具，用户不需要联网注册，数据存本地"

## Step 1-2: 裁判追问 + 方案设计

```
Q1: 目标用户是什么人群？
A1: 写作者、Prompt 工程师、AI 爱好者

Q2: 他们现在怎么解决的？
A2: 用 ChatGPT 网页版、Notion 模板、或者手写

Q3: 技术栈偏好？
A3: 前端 React/Electron，后端轻量，数据库 SQLite，Rust 加速

Q4: 预算和时间？
A4: 个人开发，3 个月内出 MVP

Q5: 盈利方式？
A5: 基础版免费，Pro 版按月订阅
```

## 初版方案 (摘要)

**方案: TipAi Desktop v1.0**

- **问题**: 现有提示词工具依赖云端，隐私差、无法离线
- **方案**: Electron + React + Rust 原生引擎的本地桌面应用
- **竞品**: PromptPerfect(云端付费)/ChatGPT(通用)/自定义模板(无 AI)
- **技术**: React 19 / Electron 41 / Rust NAPI-RS / SQLite / IPC
- **盈利**: 免费基础版 + Pro $5/月

## Step 2.5: 深度设计（裁判输出）

```
👨‍⚖️ 裁判: "方案初稿已完成。是否需要我进一步做深度设计？"

用户: "做深度设计"
```

### 创新功能点
| 功能 | 解决的问题 | 实现思路 | 用户价值 | 优先级 |
|------|-----------|---------|---------|--------|
| 智能变量补全 | 手动写变量易出错 | 输入 `{{` 时检索历史变量+AI 建议 | 减少 50% 提示词编写时间 | P0 |
| 提示词版本对比 | 改了几版不知道哪个好 | 左右分屏 diff + 内置评分 | A/B 测试快速迭代 | P1 |
| 本地 RAG 知识库 | 提示词缺乏上下文 | 本地向量索引，注入相关片段 | 提示词质量提升 | P2 |

### 最佳架构设计
**推荐模式**: Hexagonal Architecture (端口-适配器)

```
┌─────────────┐
│  Renderer   │ ← React 19, Zustand
├─────────────┤
│  Preload    │ ← contextBridge + IPC 类型安全
├─────────────┤
│  Main       │ ← Electron, IPC 路由
├─────────────┤
│  Domain     │ ← Rust NAPI-RS (prompts/settings/search)
├─────────────┤
│  Infra      │ ← SQLite (via Rust), 文件系统
└─────────────┘
```

**关键决策**: Rust 承载所有核心逻辑（CRUD/加密/搜索），Node 层仅做 IPC 路由和窗口管理。不与 SQLite 从 Node 端直接通信。

### 最佳性能实践
| 层级 | 策略 | 预期收益 |
|------|------|---------|
| 前端 | React.lazy 代码分割 + 虚拟列表 | 首屏 <1s |
| 后端 | Rust 连接池复用 + 内存缓存 | 查询 <10ms |
| 数据库 | WAL 模式 + 复合索引 | 写入不阻塞读取 |
| 基础设施 | ASAR 压缩 + 增量更新 | 安装包 <80MB |

### 最佳功能实践
| 领域 | 必做项 |
|------|--------|
| 安全性 | NaCl 加密 API Key 存储 |
| 可观测性 | 本地日志文件 + 按日滚动 |
| 用户体验 | 加载骨架屏 / 空态引导 / 错误 toast |

## Step 4: 答辩片段

```
第1轮 — 技术可行性
✅ 正方: Electron+Rust 已被 Obsidian/VS Code 验证，本地 SQLite 性能足够
❌ 反方: Rust 跨平台编译复杂，100MB+ 体积用户下载门槛高。
       建议: 先出 Windows 版，占桌面 70%+
✅ 正方: 同意。首批仅 Windows，集中验证核心体验
👨‍⚖️ 裁判: 正方 8/10 反方 7/10。修正：首批仅 Windows

第5轮 — 盈利性
✅ 正方: $5/月订阅，1000 用户即可月入 $5000 维持开发
❌ 反方: 提示词工具小众，竞品价格参考：PromptPerfect $10/月仅有 500 付费用户。
       建议: 先免费获取 10000 用户，再转化付费。
✅ 正方: 同意免费增值策略。MVP 阶段全免费，达 5000 用户后测试付费墙
👨‍⚖️ 裁判: 正方 7/10 反方 8/10。修正：MVP 免费，付费墙延迟
```

## Step 5: 裁决

```
🏆 判定: 🟢 通过 (有条件)

修改点:
1. 首批仅 Windows
2. MVP 阶段全免费
3. Pro 定价暂定 $5/月，用户达 5000 后测试

正方 7.5/10  反方 7.5/10  达成共识 80%
```

## Step 6: 原子任务拆解

> 任务总数: 27 | 适用于 AI Agent 顺序执行

**技术栈**: React 19 / Electron 41 / Rust NAPI-RS / SQLite / TypeScript / Vite

---

### Phase 1: 项目骨架

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T001 | 初始化 Electron + Vite + React 项目 | `package.json` `vite.config.ts` `electron-builder.yml` | 无 | `pnpm dev` 启动 Electron 窗口 |
| T002 | 配置 TypeScript 严格模式 + 路径别名 `@/` | `tsconfig.json` `tsconfig.node.json` | T001 | `import @/shared/types` 解析正确 |
| T003 | 配置 ESLint + Prettier | `.eslintrc.cjs` `.prettierrc` | T001 | `pnpm lint` 零错误 |
| T004 | 创建目录结构 | `src/main/` `src/renderer/` `src/preload/` `src/shared/` `src/rust/` | T001 | 5 个目录存在，含 `.gitkeep` |
| T005 | 配置 Electron main process (窗口创建+生命周期) | `src/main/index.ts` `src/main/window.ts` | T004 | 窗口 1200x800 显示，devtools 可用 |
| T006 | 配置 Electron preload (contextBridge + ipcRenderer) | `src/preload/index.ts` | T004 | `window.electronAPI` 类型定义正确 |
| T007 | 初始化 Rust NAPI-RS 编译环境 | `src/rust/Cargo.toml` `src/rust/src/lib.rs` | T004 | `pnpm build:rust` 编译出 `.node` 文件 |

### Phase 1 验证卡

| ID | 存在性 | 质量 | 防伪 | 依赖 | 测试 | 状态 | 完成时间 | 执行模型 |
|----|--------|------|------|------|------|------|---------|---------|
| T001 | `package.json` 含 electron+vite+react | `pnpm dev` 无报错 | 非 `npx create` 默认模板 | 无 | `pnpm dev` 窗口出现 | ✅ | [待填写] | [待填写] |
| T002 | `tsconfig.json` paths 含 `@/*` | `tsc --noEmit` 零错误 | `import @/shared/types` 可跳转 | T001 ✅ | `tsc --noEmit` 通过 | ✅ | [待填写] | [待填写] |
| T003 | `.eslintrc.cjs` `.prettierrc` 存在 | `pnpm lint` 零错误 | lint 规则非空配置 | T001 ✅ | `pnpm lint` 返回 0 | ✅ | [待填写] | [待填写] |
| T004 | 5 个目录存在 | 目录名无拼写错误 | 每个目录有 `.gitkeep` | T001 ✅ | `ls src/*/` 各有内容 | ✅ | [待填写] | [待填写] |
| T005 | `src/main/index.ts` `window.ts` | `tsc` 通过 | `window.ts` 非空函数体 | T004 ✅ | `pnpm dev` 窗口 1200x800 | ✅ | [待填写] | [待填写] |
| T006 | `src/preload/index.ts` | `tsc` 通过 | `contextBridge` 有实际 API | T004 ✅ | `window.electronAPI` 非 undefined | ✅ | [待填写] | [待填写] |
| T007 | `Cargo.toml` `lib.rs` | `cargo check` 通过 | `lib.rs` 含 `#[napi]` 导出 | T004 ✅ | `pnpm build:rust` 退出码 0 | ✅ | [待填写] | [待填写] |

---

### Phase 2: 基础组件

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T008 | 创建 TypeScript 共享类型 (IPC 通道枚举/提示词/设置) | `src/shared/types.ts` `src/shared/ipc-channels.ts` | T002 | 类型导入无报错 |
| T009 | 创建 IPC 调用封装 (renderer 端) | `src/renderer/lib/ipc.ts` | T006 T008 | `ipc.invoke('prompt:list')` 类型推断正确 |
| T010 | 创建 Button / Input / Modal 基础组件 | `src/renderer/components/ui/Button.tsx` `Input.tsx` `Modal.tsx` | T001 | Storybook 或页面中渲染正常 |
| T011 | 创建 Layout 组件 (侧边栏+内容区) | `src/renderer/components/Layout.tsx` `Sidebar.tsx` | T010 | 侧边栏 240px，内容区自适应 |
| T012 | 创建主题变量 (亮/暗) + ThemeProvider | `src/renderer/styles/theme.css` `ThemeProvider.tsx` | T001 | 切换 class `.dark` 后颜色变化 |
| T013 | 创建全局样式 reset + 字体 | `src/renderer/styles/global.css` | T001 | 页面加载后字体为系统默认 sans-serif |

---

### Phase 3: 数据层

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T014 | 定义 SQLite 表结构 (prompts/settings/history) | `src/rust/src/schema.rs` | T007 | `CREATE TABLE` 语句可在 SQLite 执行 |
| T015 | 实现 Rust SQLite 连接池 + 初始化建表 | `src/rust/src/db.rs` | T014 | 首次启动自动建表，无报错 |
| T016 | 实现 Rust prompts CRUD (neon 绑定到 Node) | `src/rust/src/prompts.rs` | T015 | Node 端调用 `list/create/update/delete` 返回正确 |
| T017 | 实现 Rust settings 加密读写 | `src/rust/src/settings.rs` | T015 | API key 写入后以密文存储，读取时解密 |
| T018 | 创建 main process IPC handlers (路由调用 Rust) | `src/main/ipc-handlers.ts` | T005 T016 T017 | renderer 调用 `prompt:list` 返回数据 |
| T019 | 创建前端 API Client (封装所有 IPC 调用) | `src/renderer/api/prompts.ts` `src/renderer/api/settings.ts` | T009 T018 | `api.prompts.list()` 返回 `Prompt[]` |

---

### Phase 4: 页面

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T020 | 提示词列表页 - 布局骨架 | `src/renderer/pages/PromptList/index.tsx` | T011 | 页面可访问，列表区域+搜索框+新建按钮 |
| T021 | 提示词列表页 - 交互逻辑 (搜索/删除确认/点击进入编辑) | `src/renderer/pages/PromptList/` | T020 | 搜索过滤、删除弹窗确认 |
| T022 | 提示词列表页 - 数据对接 (加载/空态/错误态) | `src/renderer/pages/PromptList/` | T021 T019 | 列表加载中 spinner → 空态"暂无提示词"→ 错误 toast |
| T023 | 提示词编辑页 - 布局骨架 (编辑器+变量面板+保存按钮) | `src/renderer/pages/PromptEditor/index.tsx` | T011 | 页面可访问，textarea+变量面板区域 |
| T024 | 提示词编辑页 - 交互逻辑 (编辑/变量插入/标签管理) | `src/renderer/pages/PromptEditor/` | T023 | 输入 `{{` 弹出变量列表，选择后插入 |
| T025 | 提示词编辑页 - 数据对接 (新建/保存/加载已有) | `src/renderer/pages/PromptEditor/` | T024 T019 | 保存后列表页刷新可见 |
| T026 | 设置页 - 布局骨架 (表单区域+分组) | `src/renderer/pages/Settings/index.tsx` | T011 | API Key 输入框+主题切换开关 |
| T027 | 设置页 - 交互逻辑 (Key 可见性切换/主题切换/校验) | `src/renderer/pages/Settings/` | T026 | 点击眼睛图标切换 Key 显示/隐藏 |
| T028 | 设置页 - 数据对接 (加密保存/读取) | `src/renderer/pages/Settings/` | T027 T019 | 重启后 Key 仍在，解密正确 |

### Phase 4 验证卡

| ID | 存在性 | 质量 | 防伪 | 依赖 | 测试 | 状态 | 完成时间 | 执行模型 |
|----|--------|------|------|------|------|------|---------|---------|
| T020 | `PromptList/index.tsx` | `tsc`+lint 通过 | 含 `<ul>` 非空 `<div>` | T011 ✅ | 导航到 `/` 显示列表 | ✅ | [待填写] | [待填写] |
| T021 | 搜索/删除逻辑代码 | lint 通过 | `onSearch` 非空函数 | T020 ✅ | 搜索过滤+删除确认 | ✅ | [待填写] | [待填写] |
| T022 | loading/empty/error 三态 | lint 通过 | 三态独立 UI 分支 | T021 ✅ | 空列表显示引导 | ✅ | [待填写] | [待填写] |
| T023 | `PromptEditor/index.tsx` | lint 通过 | `<textarea>`+`<aside>` 非空 | T011 ✅ | 编辑器+变量面板渲染 | ✅ | [待填写] | [待填写] |
| T024 | 变量插入逻辑 | lint 通过 | `insertVariable()` 操作 DOM | T023 ✅ | `{{` →变量列表→插入 | ✅ | [待填写] | [待填写] |
| T025 | 保存/加载逻辑 | lint 通过 | `handleSave` 调用 api 非 mock | T024 ✅ | 保存→列表页可见 | ✅ | [待填写] | [待填写] |
| T026 | `Settings/index.tsx` | lint 通过 | `<input type=password>` | T011 ✅ | Key 输入框+主题开关 | ✅ | [待填写] | [待填写] |
| T027 | Key 可见性/主题切换 | lint 通过 | `toggleVisibility` 切换 type | T026 ✅ | 眼睛图标切换可见性 | ✅ | [待填写] | [待填写] |
| T028 | 加密存储逻辑 | lint 通过 | `getSettings` 非明文返回 | T027 ✅ | 重启 Key 自动填充 | ✅ | [待填写] | [待填写] |

### 执行日志

```
📋 执行日志
开始时间: [待填写]
结束时间: [待填写]
执行模型: [待填写]
总任务数: 38  完成: 0  跳过: 0  失败: 0
```

---

### Phase 5: 集成联调

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T029 | React Router 路由配置 (列表/编辑/设置/404) | `src/renderer/router.tsx` | T020 T023 T026 | URL 变化页面切换正确，404 兜底 |
| T030 | 全局状态管理 (Zustand: prompts/settings/theme) | `src/renderer/store/index.ts` | T019 | DevTools 中可见 store 状态变化 |
| T031 | 页面间导航 + 列表→编辑→返回 数据流贯通 | `src/renderer/App.tsx` | T029 T030 | 列表点击→编辑页正确加载→保存返回 |
| T032 | 快捷键注册 (Cmd+N 新建 / Cmd+K 搜索 / Esc 返回) | `src/renderer/hooks/useHotkeys.ts` | T031 | 按 Cmd+N 弹出新建窗口 |

---

### Phase 6: 测试与部署

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T033 | Rust CRUD 单元测试 | `src/rust/src/prompts.rs` (追加 `#[cfg(test)]`) | T016 | `cargo test` 全部通过 |
| T034 | Rust 加密测试 | `src/rust/src/settings.rs` (追加测试) | T017 | 加密→解密 roundtrip 断言通过 |
| T035 | React 组件测试 (PromptList 渲染/搜索/空态) | `src/renderer/pages/PromptList/__tests__/` | T022 | `pnpm vitest` 3 条用例通过 |
| T036 | React 组件测试 (PromptEditor 保存/变量插入) | `src/renderer/pages/PromptEditor/__tests__/` | T025 | 保存后调用 `api.prompts.upsert` mock |
| T037 | E2E 测试 (创建→编辑→保存→列表验证) | `tests/e2e/prompt-flow.spec.ts` | T031 | `pnpm e2e` 核心流程通过 |
| T038 | Electron 打包配置 + CI | `electron-builder.yml` `.github/workflows/build.yml` | T031 | `pnpm build` 产出 `.exe` 安装包 |

---

## 风险提示
| 风险 | 来源 | 缓解措施 |
|------|------|---------|
| Rust 跨平台编译复杂 | 答辩反方指出 | Phase 1 仅 Windows，Phase 3 再适配 Mac |
| 100MB+ 安装包用户门槛 | 答辩反方指出 | 压缩+增量更新，竞品 Obsidian 120MB 用户接受 |
| 提示词工具小众，付费转化低 | 答辩正方让步 | MVP 全免费，5000 用户后测试付费墙 |
