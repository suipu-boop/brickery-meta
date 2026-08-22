---
AIGC:
    Label: "1"
    ContentProducer: 001191440300708461136T1XGW3
    ProduceID: 1ff3ab34626ddcd667748776b4e29487_a3e1e73b9e2511f1a65b525400826444
    ReservedCode1: t73w4jU/EBvisOqjXwyR0PaDYXCdolQQbAQUqoQYFFm6yaY2GT0QPs31vv/z0lBfQlVP4X1jdY5bcqNsQ3aXprpFAoqWLmC1xKZSsyyUJLcv7qVCmZ3BgMBE3laMkIwnIvZUEzqUWFGT59y7DRApfM8T/YTegQFfpJnKScbfQhXT1FRN/oIWdZ5a0Ao=
    ContentPropagator: 001191440300708461136T1XGW3
    PropagateID: 1ff3ab34626ddcd667748776b4e29487_a3e1e73b9e2511f1a65b525400826444
    ReservedCode2: t73w4jU/EBvisOqjXwyR0PaDYXCdolQQbAQUqoQYFFm6yaY2GT0QPs31vv/z0lBfQlVP4X1jdY5bcqNsQ3aXprpFAoqWLmC1xKZSsyyUJLcv7qVCmZ3BgMBE3laMkIwnIvZUEzqUWFGT59y7DRApfM8T/YTegQFfpJnKScbfQhXT1FRN/oIWdZ5a0Ao=
---

# Brickery ROADMAP

> 顶层路线图。按项目记录里程碑、当前状态与待办，各仓库内部细节见各自 specs/。

## 里程碑：v0.1.0（2026-08-22 完成）

三个交付仓库已打 tag 并发布 Release：

| 仓库 | tag | Release | 本次核心内容 |
|------|-----|---------|-------------|
| brickery | v0.1.0 | [Release](https://github.com/suipu-boop/brickery/releases/tag/v0.1.0) | recall/surface 多会话隔离（sessions 白名单）；引擎 max_tokens 12000 + 空响应重试；测试 29 例全通过 |
| brickery-factory | v0.1.0 | [Release](https://github.com/suipu-boop/brickery-factory/releases/tag/v0.1.0) | chat() 启用 memory_sessions；server.py 工作区迁 ~/.brickery/factory-vault；测试补全（test_tools 17 + test_server_api 12）；修复 macOS 符号链接 relative_to bug |
| shadeling-bricks | v0.1.0 | [Release](https://github.com/suipu-boop/shadeling-bricks/releases/tag/v0.1.0) | 新增 hello-marvis 积木（问好+报时，纯 stdlib）；index 登记 |

## v0.1.0 收尾清单（2026-08-22 全部完成）

- [x] 三仓库 tag + Release（brickery / brickery-factory / shadeling-bricks）
- [x] ROADMAP 建置（本文件）
- [x] 工坊 Pages 迁移：gh-pages 分支 + GitHub Pages 构建成功 → https://suipu-boop.github.io/brickery-workbench/
- [x] 规格同步：brickery-factory specs/brick-factory-app.md 标记已实施、brick-vault 版标记已迁出
- [x] 内核单权威干净构建：build_workbench_app.sh 全流程验证（.app + .dmg 生成，内嵌内核含 sessions 隔离）
- [x] 替换临时代理：git 直连 + gh credential helper，删除含 token 的 /tmp/git_askpass.sh
- [x] CI 自动化：brickery（内核冒烟）、brickery-factory（29 测试 + 内核依赖注入）、shadeling-bricks（12 验证闸门测试 + 全库严格验证）三仓 GitHub Actions 全部通过
- [x] 验证闸门自动化：brick-vault tests/test_verify.py 12 例（契约/元数据/files/index/composition）

## 待办（下一迭代候选）

### 高优先级

1. ~~**brickery-workbench Release 资源**~~（2026-08-22 完成）：页面链接匹配、下载 200 可用；发现附件为 09:20 旧构建（缺 16:12 factory 运行模式），已用 20:20 最新构建覆盖上传（104203004 字节与本地一致）。
2. **内核独立打包依赖**：factory 对内核仍走 BRICKERY_ROOT 本地路径注入，CI 已用 clone 兜底；下一迭代应改为 pip 依赖或 vendored 打包，去掉对本地路径的隐式依赖。

### 中优先级

1. ~~**引擎配置回退与容错**~~（2026-08-22 完成）：多会话隔离已上线，补充引擎断连/超时/限流的重试与降级策略测试（33 例全过，brickery b6e6e8e，CI 绿）。
2. ~~**skill_library fixture 缺失（既有问题）**~~（2026-08-22 完成）：根因是 list_entries 对目录型 repo_url 的 base 拼接错削一级，相对 download_url 404；已修复（brickery c0a7599），test_skill_library 13 例全过、runtime 全量 219 通过，CI 绿。
3. **工坊与工厂联调**：factory 8767 端口 + 工坊 8767 端口同为 8767，核对端口规划避免冲突（swift 壳 factory 运行模式已加，需实机验证）。

### 低优先级

1. **brick-schema 词表扩展**：category/risk_level 词表约束进 verify 闸门，与内核契约常量统一（当前校验原则"缺省安全值，声明即严格校验"）。
2. **Release 自动化发布**：CI 打通后可在 tag push 时自动构建 dmg 并发布 Release。

## 进度记录

- 2026-08-22：四项目架构定稿（ARCHITECTURE.md）；brickery/brickery-factory/shadeling-bricks 推送并发布 v0.1.0；收尾清单全部完成，三仓库 CI 全绿。
- 2026-08-22（续）：Release 资源核对完成（v0.1.0 dmg 替换为含 factory 运行模式的最新构建）；内核独立打包依赖完成（brickery pyproject + tag v0.1.1，factory pip git 依赖，BRICKERY_ROOT 注入移除，双仓库 CI 全绿）。
- 2026-08-22（续2）：引擎容错与降级专项测试 33 例完成并推送（brickery b6e6e8e，CI 绿）；发现既有问题 skill_library fixture 缺失（pdf-extractor 等，6 例失败）已记入待办。
- 2026-08-22（续3）：skill_library fixture 缺失修复完成（brickery c0a7599）：根因为目录型 repo_url 的 base 拼接错削一级，相对 download_url 404；test_skill_library 13 例全过、runtime 全量 219 通过，CI 绿。中优先级第 1、2 项完成，剩余工坊与工厂 8767 端口联调（需实机验证）。
*（内容由AI生成，仅供参考）*
