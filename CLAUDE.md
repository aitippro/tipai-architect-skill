# CLAUDE.md — tipai-architect-skill

三方 AI 答辩式方案架构师 Skill 仓库。

## 目录

```
skills/
  solution-forge.md        ← 核心 Skill（6 步工作流 + 三角色体系）
  solution-forge-lite.md   ← 轻量版（5 步，跳过答辩直接出方案）
templates/                 ← 标准输出模板（方案 / 裁决书 / 任务清单）
examples/                  ← 5 个行业完整示例
validator/
  check-tasks.md           ← 任务拆解质量检查
  check-skill.md           ← Skill 格式自检
```

## 加载 Skill

```
仓库: https://github.com/aitippro/tipai-architect-skill
Skill 文件: skills/solution-forge.md
最佳搭配: Claude Opus 4.7（复杂推理 + 长窗口）
```

## 开发规范

- 所有改动基于 `main` 分支，通过 PR 合入
- Skill 内容改动需同步更新 CHANGELOG
- 模板 / 示例 / 质检清单改动需保持与 Skill 文件一致
- 新增示例须跑通完整 6 步工作流后方可提交

## 约束

- MIT 许可（见 LICENSE）
- 禁止在 Skill / 示例中硬编码任何真实凭证或 API Key
