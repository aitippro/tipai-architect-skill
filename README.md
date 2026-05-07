<div align="center">

# TipAI Architect Skill

**三方 AI 答辩式方案架构师**

你把想法说出来 → AI 帮你设计、论证、拆成开发任务

[![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)](LICENSE)
[![Version](https://img.shields.io/badge/version-5.0-blue?style=flat-square)](CHANGELOG.md)

</div>

---

## 怎么用

### 1. 加载 Skill

在 Claude Code 中加载 Skill 文件：

```
/技能 skills/solution-forge.md
```

或直接告诉 Claude：

```
加载 https://github.com/aitippro/tipai-architect-skill/blob/main/skills/solution-forge.md
```

### 2. 说出你的想法

Skill 加载后会自动打招呼。你只需描述你想做的产品：

> "我想做一个本地优先的 AI 提示词管理桌面工具"

### 3. 坐等方案成形

AI 会自动走完 7 步流程，你只需要：
- 回答裁判的追问（可以说"跳过"）
- 预览方案时提修改意见
- 看正反方 Agent 辩论（像看节目一样）
- 最后裁决是否通过、拆解开发任务

**全程无需你主动操作，AI 自动推进。你对任何一轮不满意，说"第X轮无效，请修正"即可。**

---

## 两个模式

| | 完整版 | 轻量版 |
|---|--------|--------|
| **加载** | `/技能 skills/solution-forge.md` | `/技能 skills/solution-forge-lite.md` |
| **适用** | 商业产品、融资项目、团队协同 | 个人项目、概念验证、快速原型 |
| **流程** | 追问→方案→深度设计→答辩→裁决→拆任务 | 追问→方案→深度设计→风险自检→拆任务 |
| **辩论** | ✅ 双 Agent 公开辩论，全程可见 | ❌ 无 |
| **耗时** | 15-30 分钟 | 5-10 分钟 |

---

## 你能随时喊停或调整

| 你说 | 效果 |
|------|------|
| "跳过追问" | 直接出方案 |
| "做深度设计" | 方案追加创新功能/架构/性能设计 |
| "第 3 轮无效，赌注太大，请修正" | 废除第 3 轮，重新辩论 |
| "叫停，出裁决" | 立即结束辩论，进入裁决 |
| "通过，拆任务" | 确认方案，拆解开发任务 |
| "复庭" | 实施中遇新问题，重新开庭 |

---

## 你最终得到什么

1. **方案文档** — 技术选型、市场分析、盈利模式、风险清单
2. **答辩公告板** — 正反方辩论全程记录，所有论据带来源 URL
3. **裁决书** — 通过/驳回/有条件通过 + 裁判独立意见
4. **原子任务清单** — 按 6 阶段拆解，每个任务精确到文件级别，AI 可直接执行

---

## 质量保障

- 证据强制：论点必须附带来源 URL + 年份，无 URL 扣 2 分
- 专家证人：事实争议时 spawn 中立 AI 裁判
- 5 项验证：每个开发任务完成后必须通过存在性/质量/防伪/步骤/测试检查
- 反对意见：被驳回的论点允许反方记录保留意见

---

## 示例

想看完整流程？看这几个案例：

| 案例 | 结果 |
|------|------|
| [TipAI Desktop](examples/tipai-desktop.md) — 桌面提示词工具 | 🟢 通过 |
| [HelpFlow SaaS](examples/saas-helpdesk.md) — 客服工单系统 | 🟡 有条件通过 |
| [HabitBuddy](examples/mobile-habit-app.md) — 移动打卡 App | 🟡 有条件通过 |
| [EnvSync CLI](examples/open-source-cli-tool.md) — 开源 CLI 工具 | 🟢 通过 |
| [邻里鲜](examples/miniprogram-group-buying.md) — 社区团购小程序 | 🟡 有条件通过 |
| [FateAI](examples/rejected-proposal.md) — AI 算命（论证后否决） | 🔴 驳回 |

---

## 仓库文件

```
skills/
  solution-forge.md       ← 完整版 Skill（单文件自包含）
  solution-forge-lite.md  ← 轻量版 Skill（单文件自包含）
templates/                ← 输出模板（人类参考用）
examples/                 ← 6 个完整案例
validator/                ← 质量检查清单
```

---

<div align="center">
<sub>MIT License · TipAi Team © 2026</sub>
</div>
