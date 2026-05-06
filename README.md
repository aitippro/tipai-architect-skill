<div align="center">

# 🏛️ TipAI Architect Skill

**三方 AI 答辩式方案架构师**

用户提需求 → 裁判设计+调研 → 正方 vs 反方辩论 → 裁决 → 原子任务拆解

[![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)](LICENSE)
[![Version](https://img.shields.io/badge/version-2.0-blue?style=flat-square)](CHANGELOG.md)

</div>

---

## 📂 结构

```
tipai-architect-skill/
├── skills/
│   ├── solution-forge.md          ← 🎯 核心 Skill（完整答辩版）
│   └── solution-forge-lite.md     ← ⚡ 轻量版（跳过答辩）
├── templates/                     ← 标准输出模板
│   ├── proposal-template.md       ← 方案设计模板
│   ├── debate-board-template.md   ← 答辩公告板模板
│   ├── verdict-template.md        ← 裁决书模板
│   └── task-list-template.md      ← 原子任务清单模板
├── examples/                      ← 行业示例（6 个）
│   ├── tipai-desktop.md           ← 桌面应用（🟢 通过）
│   ├── saas-helpdesk.md           ← SaaS 客服（🟡 有条件）
│   ├── mobile-habit-app.md        ← 移动打卡（🟡 有条件）
│   ├── open-source-cli-tool.md    ← 开源 CLI（🟢 通过）
│   ├── miniprogram-group-buying.md← 社区团购（🟡 有条件）
│   └── rejected-proposal.md       ← AI 算命（🔴 驳回）
├── validator/
│   ├── check-tasks.md             ← 任务拆解质检
│   └── check-skill.md             ← Skill 格式自检
├── CHANGELOG.md
├── LICENSE
└── README.md
```

---

## 🎯 核心机制

一个 Skill + 双 Agent 隔离答辩：

| 👨‍⚖️ 裁判 (主 Agent) | ✅ 正方 (独立 Agent) | ❌ 反方 (独立 Agent) |
|:---|:---|:---|
| 追问需求、调研市场 | 为方案辩护、举证 | 寻找漏洞、质疑可行性 |
| 深度设计(可选)、主持答辩 | 写入公告板立论 | 写入公告板反驳+替代方案 |
| 裁决赛果、拆解原子任务 | 思维隔离，不知反方思考 | 思维隔离，不知正方思考 |

> **答辩通过公告板 MD 文件通信，两 Agent 完全隔离，客户全程可视。**

---

## 🚀 使用

```
1. 加载 skills/solution-forge.md 到你的 Agent
2. 告诉 Agent: "我有一个想法，帮我做方案设计"
3. Agent 自动执行 6 步工作流
```

详见 [完整示例](examples/tipai-desktop.md)

---

## 🛡️ 质量机制

| 机制 | 说明 |
|------|------|
| 答辩防空洞 | 引用注明年份来源；AI 名无效；空洞论点扣 2 分 |
| 建设性反对 | 反方不能只否定，每轮须提替代方案 |
| 5 轮小结 | 裁判每 5 轮总结进度，防原地打转 |
| SMART 任务 | 每项: Specific/Measurable/Actionable/Relevant/Time-bound |
| 检查清单 | [validator/check-tasks.md](validator/check-tasks.md) 6 类验证 |

---

## 📖 工作流

```
需求采集(追问5轮+) → 调研设计(竞品+技术+市场) → [深度设计(可选)]
    → 方案预览 → 双Agent答辩(公告板通信/思维隔离) → 裁决 → 任务拆解 → 质检
```

---

<div align="center">
<sub>非商业使用许可 · TipAi Team © 2026</sub>
</div>
