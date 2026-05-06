# Changelog

## v5.0 (2026-05-07)
- 7 项答辩机制强化：调研权/专家证人/证据附件/修改日志/反对意见书/复庭/失败示例
- Agent 人格化：正方(嘴贱)/反方(毒舌)/裁判(幽默控场)，答辩有观赏性
- 自动执行 30 轮：无需每轮请求用户确认，用户随时"第 X 轮无效"干预
- 新增 examples/rejected-proposal.md：🔴 被驳回方案全流程示例
- 公告板新增证据表/专家证言/5轮阶段性总结/证据验证
- 方案模板新增修改日志章节

## v4.0 (2026-05-06)
- 双 Agent 答辩架构：正方/反方作为独立 Agent 运行，思维隔离
- 新增 templates/debate-board-template.md：答辩公告板模板（全程记录，客户可视）
- 新增 Step 2.5 深度设计(可选)：裁判输出创新功能/架构/性能/实践 4 维设计
- Skill 重写 Step 4 为双 Agent 模式，含 Agent 生成指令格式

## v3.0 (2026-05-06)
- 新增 validator/check-skill.md：Skill 格式校验清单
- 新增 skills/solution-forge-lite.md：轻量版 Skill（跳过答辩，快速出方案）
- 新增 4 个行业示例：SaaS 客服/移动打卡/开源 CLI/社区团购
- 新增 CI：markdown lint + 链接有效性检查
- 重构 pre-commit hook，迁至 scripts/hooks/，修复自检误报
- 加固 .gitignore（.claude/ .env* 等 6 项）
- 新增 CLAUDE.md 仓库指引
- 补打 v1.0 / v2.0 git tag

## v2.0 (2026-05-05)
- 新增质量守则：答辩防空洞规则、SMART 任务标准
- 新增 templates/：方案模板、裁决书模板、任务清单模板
- 新增 examples/tipai-desktop.md：完整全流程示例
- 新增 validator/check-tasks.md：任务拆解质量检查清单
- 新增 CHANGELOG.md

## v1.0 (2026-05-05)
- 初始发布：三方答辩式方案锻造 Skill
- skills/solution-forge.md：6 步工作流
- 裁判 AI + 正方 AI + 反方 AI 三角色体系
