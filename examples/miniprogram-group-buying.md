# 示例: 微信小程序社区团购

## 用户输入
> "我想在三四线城市做一个社区团购小程序，主打本地生鲜次日达"

## 初版方案 (摘要)

**方案: 邻里鲜 v1.0**
- **问题**: 三四线城市生鲜电商渗透率低，传统团购靠微信群人工接龙
- **方案**: 微信小程序+云开发，团长端+用户端双端，次日达
- **技术**: 原生小程序 / CloudBase(数据库+云函数) / 微信支付 / 订阅消息
- **盈利**: 商品差价 15-20% + 团长佣金 12%

## 答辩裁决

```
🏆 判定: 🟡 有条件通过
修改点: 30% SKU 为地方特产 / 团长分级(金牌15%/银牌12%/铜牌10%) / 首批覆盖5个小区
```

## 原子任务拆解

> 任务总数: 35 | 技术栈: 微信小程序原生 / CloudBase / 微信支付

### Phase 1: 项目骨架

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T001 | 注册小程序 AppID + 创建项目 | `project.config.json` `app.json` `app.js` `app.wxss` | 无 | 开发者工具编译通过，预览可用 |
| T002 | 初始化 CloudBase 环境 | `cloudfunctions/` 目录 + `app.js` 中 wx.cloud.init | T001 | `wx.cloud.callFunction` 不报错 |
| T003 | 配置 ESLint (微信小程序规则) | `.eslintrc.js` | T001 | `pnpm lint` 零错误 |
| T004 | 创建目录结构 | `pages/{user,leader,product,order}` `components/` `utils/` `cloudfunctions/` | T001 | 目录存在，含 `.gitkeep` |
| T005 | 配置全局样式 (主题色/字体/间距变量) | `app.wxss` `styles/theme.wxss` | T001 | 全局 class 生效 |

### Phase 2: 基础组件

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T006 | 创建商品卡片组件 (图片/标题/价格/加购按钮) | `components/product-card/` (4 文件) | T004 | 组件渲染，图片懒加载 |
| T007 | 创建底部导航栏组件 (首页/分类/购物车/我的) | `components/tab-bar/` | T004 | tab 切换页面，当前 tab 高亮 |
| T008 | 创建数量选择器组件 (+/- 按钮，带最大限制) | `components/quantity-picker/` | T004 | 点击 +/- 数量变化，min=1 max=库存 |
| T009 | 创建空态/加载/错误提示组件 | `components/empty-state/` `components/loading/` `components/error-tip/` | T004 | 各态渲染不同内容 |
| T010 | 创建团长等级徽章组件 (金牌/银牌/铜牌) | `components/leader-badge/` | T004 | 3 种等级不同颜色+图标 |
| T011 | 封装 wx.request Promise 化 + 错误处理 | `utils/request.js` | T001 | `request({url})` 返回 Promise，错误统一 toast |
| T012 | 封装 CloudBase 调用工具 (云函数 Promise 化) | `utils/cloud.js` | T002 | `callFunction({name,data})` 返回 Promise |
| T013 | 封装时间格式化/价格格式化工具 | `utils/format.js` | T001 | `formatPrice(1299)` → "¥12.99" |

### Phase 3: 数据层

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T014 | 设计 CloudBase 数据库集合 (users/products/orders/leaders/communities) | CloudBase 控制台创建集合 | T002 | 5 个集合存在 |
| T015 | 云函数: 用户登录 (wx.login + 手机号绑定) | `cloudfunctions/user-login/` | T002 T014 | 调用返回 userId + token |
| T016 | 云函数: 商品管理 CRUD (运营端用) | `cloudfunctions/product-admin/` | T014 | 创建/上架/下架/编辑商品 |
| T017 | 云函数: 商品列表查询 (按分类/小区/推荐) | `cloudfunctions/product-list/` | T014 | 分页查询 <1s 返回 |
| T018 | 云函数: 购物车 (添加/修改数量/删除/选中结算) | `cloudfunctions/cart/` | T014 | 加购→修改数量→删→结算 |
| T019 | 云函数: 下单 (库存扣减+订单生成+微信支付预下单) | `cloudfunctions/place-order/` | T014 | 下单成功返回支付参数 |
| T020 | 云函数: 团长佣金计算 (按等级+订单金额) | `cloudfunctions/leader-commission/` | T014 | 订单完成后团长佣金自动入账 |
| T021 | 云函数: 微信支付回调 (更新订单状态+库存) | `cloudfunctions/payment-callback/` | T019 | 支付成功→订单状态→待发货 |
| T022 | 云函数: 订阅消息推送 (到货提醒/取货通知) | `cloudfunctions/subscribe-push/` | T002 | 模板消息下发成功 |

### Phase 4: 页面

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T023 | 首页 - 布局 (搜索栏+轮播+商品分类+推荐列表) | `pages/index/index.*` (4 文件) | T006 T007 | 首页渲染完整 |
| T024 | 首页 - 交互 (下拉刷新/上拉加载/分类切换) | `pages/index/` | T023 | 下拉刷新列表更新，上拉加载更多 |
| T025 | 首页 - 数据对接 (加载/空态/错误) | `pages/index/` | T024 T017 | 三态覆盖 |
| T026 | 商品详情页 - 布局+交互 (图片轮播/规格选择/加购) | `pages/product/detail.*` | T006 T008 | 图片滑动+选规格+数量+加购按钮 |
| T027 | 商品详情页 - 数据对接 | `pages/product/detail.*` | T026 T017 T018 | 加载商品→加购成功 toast |
| T028 | 购物车页 - 布局+交互 (全选/编辑/改数量/去结算) | `pages/cart/index.*` | T006 T008 | 全选/反选/改数量/总价计算 |
| T029 | 购物车页 - 数据对接 | `pages/cart/index.*` | T028 T018 | 加载→空态→有数据 |
| T030 | 确认订单页 - 布局+交互 (地址/商品清单/金额/备注) | `pages/order/confirm.*` | T006 | 地址选择+商品确认+备注 |
| T031 | 确认订单页 - 支付对接 (调起微信支付) | `pages/order/confirm.*` | T030 T019 | 点支付→调起微信支付→支付成功回调 |
| T032 | 订单列表/详情页 - 布局+交互 (状态流转) | `pages/order/list.*` `pages/order/detail.*` | T006 T009 | 待付款/待发货/待取货/已完成 tab |
| T033 | 订单列表/详情页 - 数据对接 | `pages/order/` | T032 T019 T021 | 订单状态正确流转 |
| T034 | 我的页面 - 布局+交互 (用户信息/订单/团长入口/设置) | `pages/user/index.*` | T007 | 页面渲染完整 |
| T035 | 团长端 - 订单管理+佣金查看 (含核销扫码) | `pages/leader/order-manage.*` `pages/leader/commission.*` | T010 T020 | 扫码核销→订单完成→佣金更新 |

---

### 验证卡 (Phase 3 示例)

| ID | 存在性 | 质量 | 防伪 | 依赖 | 测试 | 状态 | 完成时间 | 执行模型 |
|----|--------|------|------|------|------|------|---------|---------|
| T019 | `cloudfunctions/place-order/index.js` | 云函数日志无报错 | 实际调用 `cloud.database().transaction` | T014 ✅ | 模拟下单→数据库生成订单 | ✅ | [待填写] | [待填写] |
| T021 | `cloudfunctions/payment-callback/index.js` | 云函数日志无报错 | 实际调用 `order.update` + `product.stock.decr` | T019 ✅ | 支付回调→订单状态 `paid` | ✅ | [待填写] | [待填写] |
