# 示例: 开源 CLI 开发工具

## 用户输入
> "我想做一个开源的环境变量管理 CLI 工具，解决 .env 文件安全和多环境切换问题"

## Step 1-2: 裁判追问 + 方案设计

```
Q1: 解决什么具体痛点？
A1: 团队共享 .env 靠 Slack/私聊，不安全；多环境(dev/staging/prod)切换靠手动改

Q2: 竞品了解吗？
A2: Doppler($5/seat 贵)、Infisical(自部署复杂)、dotenv-vault(功能少)

Q3: 技术栈偏好？
A3: Rust CLI，本地优先，支持自部署 sync server

Q4: 开源协议？
A4: MIT，核心功能免费，企业功能(SSO/Audit)闭源

Q5: 差异化？
A5: 极致性能(<100ms 注入)，Git-native 工作流(.env 文件加密存 git)
```

## 初版方案 (摘要)

**方案: EnvSync v1.0**

- **问题**: .env 文件非对称加密存 git，多环境切换靠手动
- **方案**: Rust CLI + 可选 sync server(Go)，git 原生集成
- **竞品**: Doppler(贵/闭源)/Infisical(重)/dotenv-vault(弱)
- **技术**: Rust CLI / Go sync server / SQLite / NaCl 加密 / Homebrew 分发
- **盈利**: 开源 MIT，企业版 License $99/年

## Step 4: 答辩片段

```
第1轮 — 技术可行性
✅ 正方: Rust 单二进制 + NaCl 加密，性能碾压 Node.js 方案
❌ 反方: NaCl 密钥管理是核心难点。用户丢了密钥=丢所有 .env。
       建议: 首次运行强制打印恢复短语(12 单词)，BIP39 标准
✅ 正方: 采纳。加入 Shamir Secret Sharing 方案

第8轮 — 开源社区
✅ 正方: CLI 工具领域 Rust 社区活跃，参考 ripgrep/fd 增长曲线
❌ 反方: 竞品 Infisical 8000+ Star 已建立心智。你的差异化在哪里？
       建议: 聚焦"git-native"这个切点，将 .env 加密存 git 作为唯一卖点
👨‍⚖️ 裁判: 正方 6/10 反方 8/10。聚焦 git-native，不做 sync server(MVP)
```

## Step 5: 裁决

```
🏆 判定: 🟢 通过

修改点:
1. MVP 只做 CLI，不包含 sync server
2. 核心卖点聚焦 "加密 .env 直接存 git"
3. 强制 BIP39 恢复短语

正方 7.0/10  反方 7.5/10  达成共识 85%
```

## Step 6: 任务拆解

| ID | 任务 | 依赖 | 验收 |
|----|------|------|------|
| T001 | Rust CLI 骨架(clap+anyhow) | 无 | `envsync --help` 输出正确 |
| T002 | NaCl 密钥生成+BIP39 短语 | 无 | 生成密钥→输出 12 单词 |
| T003 | .env 文件加密/解密 | T002 | 加密→解密→内容一致 |
| T004 | git 集成(add/commit/push 自动加密) | T003 | `git add .env.enc` 自动加密 |
| T005 | 多环境切换(dev/staging/prod) | T003 | `envsync switch prod` 切换 |
| T006 | `envsync inject` 注入到进程 | T003 | `envsync inject -- npm run dev` |
| T007 | Homebrew/Linux 一键安装脚本 | T001 | `brew install envsync` 成功 |
