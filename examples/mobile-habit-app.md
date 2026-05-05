# 示例: 移动端习惯打卡 App

## 用户输入
> "我想做一个极简的习惯打卡 App，主打社交激励，用户之间可以互相监督"

## 初版方案 (摘要)

**方案: HabitBuddy v1.0**
- **问题**: 单人打卡缺乏持续动力，现有工具偏自我管理
- **方案**: Flutter 跨平台，好友互相关注+打卡可见+轻量互动
- **技术**: Flutter 3.x / Firebase Auth+Firestore / FCM 推送 / RevenueCat
- **盈利**: 免费 + $2.99 去广告

## 答辩裁决

```
🏆 判定: 🟡 有条件通过
修改点: MVP 不含社交动态 / 必须包含桌面 Widget / 盈利推迟到 1000 DAU 后
```

## 原子任务拆解

> 任务总数: 32 | 技术栈: Flutter 3.x / Firebase Auth / Firestore / FCM / RevenueCat

### Phase 1: 项目骨架

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T001 | 创建 Flutter 项目 | `pubspec.yaml` `lib/main.dart` | 无 | `flutter run` iOS+Android 均启动 |
| T002 | 配置 Firebase (Auth + Firestore + FCM) | `google-services.json` `GoogleService-Info.plist` `firebase_options.dart` | T001 | Firebase 初始化无报错 |
| T003 | 配置 linter 规则 (flutter_lints) | `analysis_options.yaml` | T001 | `flutter analyze` 零问题 |
| T004 | 创建目录结构 | `lib/{models,services,screens,widgets,utils,providers}` | T001 | 6 个目录存在 |
| T005 | 配置环境变量 (firebase config / revenuecat key) | `lib/utils/env.dart` `.env.template` | T002 | 不硬编码密钥，从 env 读取 |

### Phase 2: 基础组件

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T006 | 创建数据模型 (Habit/User/CheckIn) | `lib/models/habit.dart` `user.dart` `checkin.dart` | T004 | `fromJson(toJson(x)) == x` 测试通过 |
| T007 | 创建基础组件 (HabitCard/StreakBadge/EmptyState) | `lib/widgets/habit_card.dart` `streak_badge.dart` `empty_state.dart` | T004 | Widget 测试渲染通过 |
| T008 | 创建 App 主题 (颜色/字体/间距) | `lib/utils/theme.dart` | T001 | 亮/暗模式切换颜色正确 |
| T009 | 创建加载/错误/空状态通用组件 | `lib/widgets/async_value_widget.dart` | T007 | 三态切换正常 |
| T010 | 创建日期工具函数 (今天/本周/连续天数) | `lib/utils/date_utils.dart` | T004 | `getStreak([])` 返回正确连续数 |

### Phase 3: 数据层

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T011 | Firebase Auth 封装 (邮箱注册/登录/登出/重置密码) | `lib/services/auth_service.dart` | T002 | 注册→登录→获取 uid |
| T012 | Firestore habits CRUD 封装 | `lib/services/habit_service.dart` | T002 T006 | 创建/读取/更新/删除 habit 文档 |
| T013 | Firestore checkins 写入封装 (含去重) | `lib/services/checkin_service.dart` | T002 T006 | 同日重复打卡被拒绝 |
| T014 | Firestore friends 封装 (搜索/关注/取关/好友列表) | `lib/services/friend_service.dart` | T002 | 搜索→发请求→接受→互为好友 |
| T015 | FCM 推送 token 注册+存储 | `lib/services/notification_service.dart` | T002 | token 写入 Firestore 用户文档 |
| T016 | RevenueCat 初始化 + 购买/恢复 | `lib/services/purchase_service.dart` | T005 | `purchaseService.isPro` 读取正确 |

### Phase 4: 页面

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T017 | 今日打卡页 - 布局骨架 (习惯列表+打卡按钮) | `lib/screens/home/home_screen.dart` | T007 | 屏幕渲染习惯列表 |
| T018 | 今日打卡页 - 交互逻辑 (点击打卡+动画+震动) | `lib/screens/home/` | T017 | 打卡→勾号动画→HapticFeedback |
| T019 | 今日打卡页 - 数据对接 (加载/空态/完成态) | `lib/screens/home/` | T018 T012 T013 | 空态引导创建→加载→打卡刷新 |
| T020 | 习惯管理页 - 布局 (新建/编辑表单) | `lib/screens/habit/habit_form_screen.dart` | T007 | 表单渲染(名称/图标/频次/颜色) |
| T021 | 习惯管理页 - 交互+数据对接 | `lib/screens/habit/` | T020 T012 | 保存→回首页可见 |
| T022 | 好友页 - 布局 (搜索栏+好友列表) | `lib/screens/friends/friends_screen.dart` | T007 | 搜索框+好友卡片列表 |
| T023 | 好友页 - 交互+数据对接 | `lib/screens/friends/` | T022 T014 | 搜索→关注→好友列表更新 |
| T024 | 好友打卡页 - 布局 (好友今日打卡列表) | `lib/screens/friends/friend_checkins_screen.dart` | T007 | 好友打卡渲染 |
| T025 | 好友打卡页 - 交互+数据对接 | `lib/screens/friends/` | T024 T013 T014 | 看到好友今日打卡（已完成/未完成） |
| T026 | 设置页 - 布局+交互 (Pro/主题/通知/账号) | `lib/screens/settings/settings_screen.dart` | T008 T011 T016 | 开关切换生效 |
| T027 | Widget 桌面小组件 (今日待打卡) | `lib/widgets/home_widget.dart` | T012 | 桌面显示今日习惯列表，点击跳转 App |
| T028 | 引导页 (首次使用 3 步引导) | `lib/screens/onboarding/onboarding_screen.dart` | T007 | 滑动 3 页→进入主页 |

### Phase 5: 集成联调

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T029 | GoRouter 路由配置 (含鉴权守卫) | `lib/utils/router.dart` | T019 T021 T023 T025 T026 T028 | 未登录→引导页，已登录→主页 |
| T030 | Provider 状态管理 (user/habits/theme) | `lib/providers/` | T011 T012 | Provider.of 读取状态正确 |
| T031 | 全流程走查 (注册→创建习惯→打卡→加好友→看好友打卡) | 手动验证 | T029 T030 | 全流程无阻断 |

### Phase 6: 测试与部署

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T032 | 数据模型测试 (Habit/User 序列化) | `test/models/` | T006 | `flutter test` 通过 |
| T033 | Widget 测试 (HomeScreen/FriendsScreen/Settings) | `test/widgets/` | T019 T023 T026 | 渲染+交互断言通过 |
| T034 | 集成测试 (创建习惯→打卡全流程) | `test/integration/` | T031 | `flutter test integration_test` 通过 |
| T035 | Firebase 安全规则配置 | `firestore.rules` | T002 | 未授权读被拒绝 |
| T036 | Fastlane 打包+发布配置 | `fastlane/Fastfile` | T031 | `fastlane beta` 上传 TestFlight |

---

### 验证卡 (Phase 4 示例)

| ID | 存在性 | 质量 | 防伪 | 依赖 | 测试 | 状态 | 完成时间 | 执行模型 |
|----|--------|------|------|------|------|------|---------|---------|
| T017 | `home_screen.dart` 存在 | `flutter analyze` 通过 | 列表渲染非空 `ListView` | T007 ✅ | 页面渲染 ≥1 个 HabitCard | ✅ | [待填写] | [待填写] |
| T019 | 数据对接代码 | `flutter analyze` 通过 | `initState` 实际调用 `habitService.list()` | T018 ✅ | 无习惯时显示 EmptyState | ✅ | [待填写] | [待填写] |
| T027 | `home_widget.dart` 存在 | iOS `WidgetKit` 编译通过 | Widget 非空占位 | T012 ✅ | 桌面添加小组件显示习惯 | ✅ | [待填写] | [待填写] |
