# 示例: 开源 CLI 开发工具

## 用户输入
> "我想做一个开源的环境变量管理 CLI 工具，解决 .env 文件安全和多环境切换问题"

## 初版方案 (摘要)

**方案: EnvSync v1.0**
- **问题**: 团队共享 .env 靠 Slack/私聊不安全，多环境切换靠手动改
- **方案**: Rust CLI，本地优先，git 原生集成，加密 .env 存 git
- **技术**: Rust CLI / NaCl 加密 / SQLite / clap / Homebrew 分发
- **盈利**: MIT 开源，企业版 License $99/年

## 答辩裁决

```
🏆 判定: 🟢 通过
修改点: MVP 只做 CLI，不做 sync server / 核心卖点聚焦 "加密 .env 存 git" / 强制 BIP39 恢复短语
```

## 原子任务拆解

> 任务总数: 30 | 技术栈: Rust / clap / NaCl (libsodium) / rusqlite / reqwest

### Phase 1: 项目骨架

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T001 | 初始化 Rust 项目 + Cargo.toml 依赖声明 | `Cargo.toml` `src/main.rs` | 无 | `cargo build` 成功 |
| T002 | 配置 clap CLI 参数解析 (init/encrypt/decrypt/inject/switch 子命令) | `src/cli.rs` | T001 | `envsync --help` 输出 5 个子命令 |
| T003 | 配置日志 (tracing-subscriber, --verbose) | `src/logging.rs` | T001 | `envsync --verbose` 输出调试日志 |
| T004 | 配置错误类型 (thiserror) | `src/error.rs` | T001 | `EnvSyncError` 含 Display + Debug |
| T005 | 创建目录结构 | `src/{cmd,crypto,db,git,utils}` | T001 | 5 个模块目录有 `mod.rs` |

### Phase 2: 基础模块

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T006 | 定义配置文件格式 (TOML) + Config struct | `src/config.rs` | T001 | `~/.config/envsync/config.toml` 解析正确 |
| T007 | 定义 .env 文件模型 (EnvVar / EnvFile) | `src/models.rs` | T001 | `EnvVar { key, value, comment }` |
| T008 | 实现文件系统工具 (查找 .env / 读写 / 权限) | `src/utils/fs.rs` | T001 | 读 `.env` → 解析为 `Vec<EnvVar>` |
| T009 | 实现终端输出美化工具 (彩色/进度条) | `src/utils/term.rs` | T001 | `info!("...")` 输出带颜色 |
| T010 | 实现 git 检测工具 (检查是否在 git repo 内) | `src/git/detect.rs` | T001 | 在 git repo 返回 true，否则 false |

### Phase 3: 数据层

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T011 | NaCl 密钥生成 (KeyPair 生成 + BIP39 12 单词) | `src/crypto/keygen.rs` | T001 | `envsync init` 生成密钥+输出 12 单词 |
| T012 | NaCl 对称加密/解密 (.env 内容) | `src/crypto/cipher.rs` | T011 | 加密→解密 roundtrip 一致 |
| T013 | NaCl 密钥文件恢复 (从 12 单词恢复密钥) | `src/crypto/recover.rs` | T011 | 输入 12 单词→恢复密钥→解密成功 |
| T014 | SQLite 本地状态库 (环境/文件映射/操作日志) | `src/db/mod.rs` `src/db/schema.rs` | T001 | 首次运行自动建表 |
| T015 | 多环境配置管理 (dev/staging/prod 切换) | `src/cmd/switch.rs` | T007 T014 | `envsync switch prod` 切换环境变量 |
| T016 | .env.enc 文件格式定义 + 序列化/反序列化 | `src/crypto/format.rs` | T012 | `.env.enc` 文件含 nonce+ciphertext+mac |

### Phase 4: 命令实现

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T017 | `envsync init` 命令 (生成密钥+创建 .env.enc+输出恢复短语) | `src/cmd/init.rs` | T011 T016 | 运行后在 git repo 生成 `.env.enc` |
| T018 | `envsync encrypt` 命令 (读取 .env → 加密写入 .env.enc) | `src/cmd/encrypt.rs` | T012 T016 | `.env` → `.env.enc` 文件大小变化 |
| T019 | `envsync decrypt` 命令 (读取 .env.enc → 解密输出到终端) | `src/cmd/decrypt.rs` | T012 T016 | 解密输出与原始 .env 内容一致 |
| T020 | `envsync inject` 命令 (解密 → 注入当前进程 → 运行子命令) | `src/cmd/inject.rs` | T012 T015 | `envsync inject -- npm run dev` 环境变量生效 |
| T021 | `envsync switch` 命令 (切换当前环境) | `src/cmd/switch.rs` | T014 T015 | 切换后 `envsync inject` 使用新环境 |
| T022 | `envsync add` 命令 (向当前环境添加变量) | `src/cmd/add.rs` | T018 | `envsync add KEY=VALUE` 后加密存储 |
| T023 | `envsync list` 命令 (列出当前环境所有变量名) | `src/cmd/list.rs` | T019 | 输出变量名列表(不输出值) |
| T024 | `envsync recover` 命令 (从 12 单词恢复) | `src/cmd/recover.rs` | T013 | 恢复后可正常 inject |

### Phase 5: 集成

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T025 | 主入口串联所有子命令 | `src/main.rs` | T017-T024 | `envsync <subcommand>` 全部可用 |
| T026 | Git pre-commit hook 自动加密集成 | `src/git/hook.rs` | T010 T018 | `git commit` 时自动执行 encrypt |
| T027 | CI 模板生成 (GitHub Actions 使用 envsync inject) | `src/cmd/ci.rs` | T020 | `envsync ci-init` 输出 GitHub Actions YAML |

### Phase 6: 测试与分发

| ID | 任务 | 产出文件 | 依赖 | 验收标准 |
|----|------|---------|------|---------|
| T028 | 加密 roundtrip 单元测试 (所有 cipher 路径) | `src/crypto/cipher.rs` (追加 `#[cfg(test)]`) | T012 | `cargo test` 加密测试全部通过 |
| T029 | 命令集成测试 (init→add→encrypt→decrypt→inject→switch) | `tests/integration_test.rs` | T025 | `cargo test --test integration_test` 通过 |
| T030 | CLI 测试 (assert_cmd: 每个子命令 help+基本调用) | `tests/cli_test.rs` | T025 | `cargo test --test cli_test` 通过 |
| T031 | 跨平台编译验证 (macOS/Linux) | `Makefile` `.github/workflows/release.yml` | T025 | CI 产出 3 平台二进制 |
| T032 | Homebrew formula 模板 | `Formula/envsync.rb.template` | T031 | `brew install envsync` 成功后运行可用 |

---

### 验证卡 (Phase 4 示例)

| ID | 存在性 | 质量 | 防伪 | 依赖 | 测试 | 状态 | 完成时间 | 执行模型 |
|----|--------|------|------|------|------|------|---------|---------|
| T017 | `src/cmd/init.rs` 存在 | `cargo clippy` 通过 | 实际写文件，非 `println!` | T011 ✅ | `envsync init` 生成 `.env.enc` | ✅ | [待填写] | [待填写] |
| T018 | `src/cmd/encrypt.rs` 存在 | `cargo clippy` 通过 | 调用 `cipher::encrypt()` 非占位 | T012 ✅ | 加密后 hex 与原文不同 | ✅ | [待填写] | [待填写] |
| T020 | `src/cmd/inject.rs` 存在 | `cargo clippy` 通过 | `set_var`+`Command.envs()` | T012 ✅ | `envsync inject -- env` 含注入变量 | ✅ | [待填写] | [待填写] |
