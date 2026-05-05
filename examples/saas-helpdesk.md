# 示例: SaaS 客服工单系统

## 用户输入
> "我想做一个面向中小团队的 SaaS 客服工单系统，支持多渠道接入"

## 初版方案 (摘要)

**方案: HelpFlow SaaS v1.0**
- **问题**: 中小企业用不起 Zendesk($49/人/月起)，开源方案维护成本高
- **方案**: Next.js 全栈 SaaS，AI 驱动工单分类+自动回复建议
- **技术**: Next.js 15 / Prisma+PostgreSQL / tRPC / OpenAI API / Stripe
- **盈利**: $29/月起，3 人免费

## 答辩裁决

```
🏆 判定: 🟡 有条件通过
修改点: MVP 单租户 tenant_id 隔离 / AI 功能 Phase 2 / 单一套餐 $29/月
```

## 原子任务拆解

> 任务总数: 35 | 技术栈: Next.js 15 / Prisma / PostgreSQL / tRPC / NextAuth / Stripe / Tailwind

### Phase 1: 项目骨架

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T001 | 初始化 Next.js 项目 + TypeScript | `package.json` `next.config.ts` `tsconfig.json` | 无 | `pnpm dev` 页面渲染 |
| T002 | 配置路径别名 `@/` + Tailwind | `tsconfig.json` `tailwind.config.ts` `postcss.config.mjs` | T001 | `import @/lib/` 解析，Tailwind class 生效 |
| T003 | 配置 ESLint + Prettier | `.eslintrc.json` `.prettierrc` | T001 | `pnpm lint` 零错误 |
| T004 | 创建目录结构 | `src/{app,components,lib,types,server}` `prisma/` | T001 | 目录存在 |
| T005 | Prisma 初始化 + PostgreSQL 连接字符串 | `prisma/schema.prisma` `.env` | T004 | `pnpm prisma validate` 通过 |

### Phase 2: 基础组件

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T006 | 创建 TypeScript 类型定义 | `src/types/index.ts` | T002 | 类型导入无报错 |
| T007 | 创建 UI 基础组件 (Button/Input/Select/Modal/Table/Toast) | `src/components/ui/` (6 个文件) | T002 | Storybook 或页面渲染正常 |
| T008 | 创建 Layout 组件 (顶部导航+侧边栏+内容区) | `src/components/Layout.tsx` `Navbar.tsx` `Sidebar.tsx` | T007 | 登录后显示布局 |
| T009 | 创建 EmptyState / Loading / Error 三态组件 | `src/components/ui/EmptyState.tsx` `Loading.tsx` `ErrorBox.tsx` | T007 | 各态渲染不同内容 |

### Phase 3: 数据层

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T010 | 定义 Prisma Schema (User/Workspace/Ticket/TicketComment) | `prisma/schema.prisma` | T005 | `prisma db push` 成功建表 |
| T011 | Prisma 迁移文件 | `prisma/migrations/` | T010 | `prisma migrate dev` 无错误 |
| T012 | 创建 tRPC router 骨架 + context (含 Prisma client) | `src/server/trpc.ts` `src/server/context.ts` | T010 | tRPC context 含 `prisma` |
| T013 | 实现 Auth router (注册/登录/登出/session) - NextAuth | `src/server/routers/auth.ts` | T012 | `POST /api/auth/signup` 返回 200 + session |
| T014 | 实现 Workspace router (创建/加入/成员管理) | `src/server/routers/workspace.ts` | T012 | 创建 workspace → 成员列表可见 |
| T015 | 实现 Ticket router (CRUD + 状态流转 + 分配) | `src/server/routers/ticket.ts` | T012 | 创建→open→in_progress→resolved 状态流转 |
| T016 | 实现 Comment router (内部备注 + 对外回复) | `src/server/routers/comment.ts` | T015 | 评论关联 ticket 正确 |
| T017 | 创建前端 tRPC Client + React Query 封装 | `src/lib/trpc.ts` `src/components/TrpcProvider.tsx` | T012 | 前端可调用 `trpc.auth.me.useQuery()` |
| T018 | 实现 Stripe Checkout Session API | `src/server/routers/billing.ts` `src/lib/stripe.ts` | T012 | Stripe webhook 接收并更新订阅状态 |

### Phase 4: 页面

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T019 | 注册页 - 布局骨架 | `src/app/(auth)/signup/page.tsx` | T008 | 表单渲染 |
| T020 | 注册页 - 交互逻辑 (校验+密码强度) | `src/app/(auth)/signup/` | T019 | 邮箱格式校验+密码 ≥8 字符 |
| T021 | 注册页 - 数据对接 | `src/app/(auth)/signup/` | T020 T013 | 注册成功跳转 `/dashboard` |
| T022 | 登录页 (布局+交互+对接合并) | `src/app/(auth)/login/page.tsx` | T008 T013 | 登录成功跳转 |
| T023 | Dashboard 仪表盘 - 布局骨架 | `src/app/(app)/dashboard/page.tsx` | T008 | 统计卡片区域渲染 |
| T024 | Dashboard - 交互逻辑 (时间筛选+图表) | `src/app/(app)/dashboard/` | T023 T017 | 日期范围切换图表更新 |
| T025 | Dashboard - 数据对接 | `src/app/(app)/dashboard/` | T024 | 实时工单数/解决率/平均响应时间 |
| T026 | 工单列表页 - 布局+列表渲染 | `src/app/(app)/tickets/page.tsx` | T008 T009 | 表格+筛选栏+新建按钮 |
| T027 | 工单列表页 - 交互逻辑 (搜索/过滤/排序/分页) | `src/app/(app)/tickets/` | T026 | 搜索过滤 <500ms 响应 |
| T028 | 工单列表页 - 数据对接 | `src/app/(app)/tickets/` | T027 T017 | 加载中/空态/错误三态覆盖 |
| T029 | 工单详情页 - 布局 (工单信息+评论列表+回复框) | `src/app/(app)/tickets/[id]/page.tsx` | T008 | 页面渲染工单详情 |
| T030 | 工单详情页 - 交互 (状态按钮/指派/优先级/标签) | `src/app/(app)/tickets/[id]/` | T029 | 点状态按钮流转，指派下拉选人 |
| T031 | 工单详情页 - 数据对接 | `src/app/(app)/tickets/[id]/` | T030 T017 | 刷新后数据保持 |
| T032 | 设置页 - 工作区设置 (名称/Logo/成员管理) | `src/app/(app)/settings/page.tsx` | T008 T017 | 保存设置生效 |

### Phase 5: 集成联调

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T033 | NextAuth 鉴权守卫 (middleware) | `src/middleware.ts` | T013 T029 | 未登录 → 跳转登录页 |
| T034 | 路由串联 (所有页面导航测试) | `src/app/(app)/layout.tsx` | T029 T030 T031 T032 | 侧边栏导航点击跳转正确 |
| T035 | 全流程 E2E 走查 (注册→创建 workspace→创建工单→回复→关闭) | 手动验证 | T033 T034 | 全流程无阻断 |

### Phase 6: 测试与部署

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T036 | tRPC router 单元测试 (auth/ticket/comment) | `src/server/__tests__/` | T013 T015 T016 | `pnpm test` 覆盖率 >80% |
| T037 | 组件测试 (工单列表/详情/登录) | `src/app/__tests__/` | T028 T031 | 渲染+交互断言通过 |
| T038 | E2E 测试 (核心流程 Playwright) | `tests/e2e/` | T035 | `pnpm e2e` 3 条用例通过 |
| T039 | Vercel 部署配置 | `vercel.json` | T035 | `vercel deploy` 成功 |
| T040 | CI: lint + typecheck + test + e2e | `.github/workflows/ci.yml` | T003 T036 T037 T038 | PR 触发 CI，全部通过 |

---

### 验证卡 (Phase 3 示例)

| ID | 存在性 | 质量 | 防伪 | 依赖 | 测试 | 状态 | 完成时间 | 执行模型 |
|----|--------|------|------|------|------|------|---------|---------|
| T010 | `schema.prisma` 含 4 个 model | `prisma validate` 通过 | model 含实际字段，非空 model | T005 ✅ | `prisma db push --dry-run` 输出正确 | ✅ | [待填写] | [待填写] |
| T013 | `auth.ts` 含 register/login/logout | `tsc` 通过 | `register` 实际调用 `prisma.user.create` | T012 ✅ | `curl POST /api/auth/signup` 返回 200 | ✅ | [待填写] | [待填写] |
| T015 | `ticket.ts` 含 CRUD+status | `tsc` 通过 | `updateStatus` 校验状态机逻辑 | T012 ✅ | 创建→流转→查询状态正确 | ✅ | [待填写] | [待填写] |
