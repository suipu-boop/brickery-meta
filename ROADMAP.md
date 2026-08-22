# Brickery ROADMAP

> 顶层路线图。按项目记录里程碑、当前状态与待办，各仓库内部细节见各自 specs/。

## 里程碑：v0.1.0（2026-08-22 完成）

三个交付仓库已打 tag 并发布 Release：

| 仓库 | tag | Release | 本次核心内容 |
|------|-----|---------|-------------|
| brickery | v0.1.0 | [Release](https://github.com/suipu-boop/brickery/releases/tag/v0.1.0) | recall/surface 多会话隔离（sessions 白名单）；引擎 max_tokens 12000 + 空响应重试；测试 29 例全通过 |
| brickery-factory | v0.1.0 | [Release](https://github.com/suipu-boop/brickery-factory/releases/tag/v0.1.0) | chat() 启用 memory_sessions；server.py 工作区迁 ~/.brickery/factory-vault；测试补全（test_tools 17 + test_server_api 12）；修复 macOS 符号链接 relative_to bug |
| shadeling-bricks | v0.1.0 | [Release](https://github.com/suipu-boop/shadeling-bricks/releases/tag/v0.1.0) | 新增 hello-marvis 积木（问好+报时，纯 stdlib）；index 登记 |

## 待办（按优先级）

### 高优先级

- 暂无。v0.1.0 收尾完成，进入下一迭代。

### 中优先级

1. **工坊 Pages 迁移**：brickery-workbench `site/` 网页下载站迁移到 GitHub Pages，废弃本地静态服务。
2. **规格同步**：四项目架构定稿已入 brickery-meta/ARCHITECTURE.md，各仓库 specs/ 中过时文档需对齐（brick-vault specs/brick-factory-app.md、brickery-factory specs/brick-factory-app.md）。
3. **内核单权威落实**：工坊构建从 GitHub 拉取内核的流程已就绪，验证一次干净构建（temp/python 准备 → build_workbench_app.sh）。

### 低优先级

1. **替换临时代理**：git 走本地 CONNECT 代理 127.0.0.1:18080 + /tmp/git_askpass.sh 凭据为临时方案，建议换正式 SSH key 或凭据管理器（当前 token 无 admin:public_key 权限，需先补权限）。
2. **CI 自动化**：三仓库接入 GitHub Actions，push 后自动跑测试 + 打 tag/Release。
3. **brick-vault 验证闸门**：积木验收闸门（brick.json 契约校验）补自动化测试。

## 进度记录

- 2026-08-22：四项目架构定稿（ARCHITECTURE.md）；brickery/brickery-factory/shadeling-bricks 推送并发布 v0.1.0。
