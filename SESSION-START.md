# SESSION-START · 三项目新会话启动提示词

> 长期复用文档（2026-08-22 建立）。本文件随项目演进更新；发生大变动时同步修改此处。
> 用法：新建会话时，直接复制下方对应提示词即可恢复全局视野。

## 通用约定（任一会话都适用）

- 称呼用户「老板」；输出禁用 emoji
- 核心代码改动先落盘 specs/*.md 供用户审阅，确认后再动手
- push / Release 上传等写操作需单独确认，不可顺手提交或上传
- 删除本地文件一律用移入回收站方式，不用 shell rm -rf
- 本机 macOS 无 GNU `timeout` 命令
- 跨项目问题按 ARCHITECTURE.md 接口契约表定位到对应仓库
- 小白友好优先：UI 直白、下载到桌面（/Users/suipu/Desktop）、批量、明确使用说明

## 会话一 · 积木工坊

```
读取 /Users/suipu/Dev/brickery-meta/ARCHITECTURE.md 了解三项目全局，
然后进入 /Users/suipu/Dev/brickery-workbench（积木工坊，面向用户的组装+分发端）：
必读 README.md、specs/project-org.md、specs/workbench-live-market.md。
核心工作区：web/（前端）、brickery/web/（工坊后端 server.py/live_vault.py）、scripts/build_workbench_app.sh（构建）。
构建产物在 output/，构建命令 scripts/build_workbench_app.sh（自动拉取生成 agent 内核合并）。
称呼我老板；输出禁用 emoji；核心代码改动先落盘 specs/*.md 供我审阅；push 前需我明确确认。
```

## 会话二 · 生成 agent

```
读取 /Users/suipu/Dev/brickery-meta/ARCHITECTURE.md 了解三项目全局，
然后进入 /Users/suipu/Dev/brickery（生成 agent：内核运行时+装配+引导+聊天+产出链路）：
必读 README.md、ROADMAP.md，规划在 specs/（hotplug、p4-packaging、setup-wizard 等）。
核心工作区：brickery/（内核：assembler/produce/package/dmg）、app/（Swift 壳，与工坊共享，改动需同步 brickery-workbench）。
称呼我老板；输出禁用 emoji；核心代码改动先落盘 specs/*.md 供我审阅；push/Release 前需我明确确认。
```

## 会话三 · 积木加工厂应用

```
读取 /Users/suipu/Dev/brickery-meta/ARCHITECTURE.md 了解四项目全局，
然后进入 /Users/suipu/Dev/brickery-factory（积木加工厂应用：独立仓库，与工坊同形态）：
必读 specs/brick-factory-app.md、specs/multi-session.md。
核心工作区：factory/（后端 server.py/agent.py/live_vault.py）、web/（前端）、scripts/build_factory_app.sh（构建）。
构建产物在 output/（BrickeryFactory.app/.dmg），8767 端口运行，打开前先确认 8767 无残留占用。
产出积木 push 进 brick-vault 后工坊/agent 才可见；工厂 agent 红线：不 push、不发布、不删已发布积木。
称呼我老板；输出禁用 emoji；核心代码改动先落盘 specs/*.md 供我审阅；push/发布前需我明确确认。
```

## 会话四 · 积木产品库

```
读取 /Users/suipu/Dev/brickery-meta/ARCHITECTURE.md 了解四项目全局，
然后进入 /Users/suipu/Dev/brick-vault（积木产品库：只留积木+验证闸门）：
必读 specs/brick-schema.md（brick.json 5 字段契约）及积木目录结构。
产出物是积木（brick.json + 内容），发布前必须跑 scripts/verify_bricks.py 过闸门。
工坊 live_vault 直连本仓库，契约变更需通知工坊侧。
称呼我老板；输出禁用 emoji；核心改动先落盘 specs/*.md 供我审阅；push 前需我明确确认。
```

## 四仓库同步状态（2026-08-22 更新）

| 仓库 | 本地路径 | GitHub | 状态 |
|------|---------|--------|------|
| 积木工坊 | /Users/suipu/Dev/brickery-workbench | suipu-boop/brickery-workbench | 已同步；Release v0.1.0 已发布（最新 dmg 104194405 字节） |
| 生成 agent | /Users/suipu/Dev/brickery | suipu-boop/brickery | 已同步 |
| 积木加工厂应用 | /Users/suipu/Dev/brickery-factory | suipu-boop/brickery-factory | 已同步（Release 未发） |
| 积木产品库 | /Users/suipu/Dev/brick-vault | suipu-boop/brick-vault | 已同步 |
| 架构总览 | /Users/suipu/Dev/brickery-meta | suipu-boop/brickery-meta | 本文件所在 |

## 发布记录（2026-08-22）

- **brickery-workbench Release v0.1.0**：三项目拆分后重新打包，上传最新 dmg（104194405 字节）
- **下载链接迁移**：site/index.html 全部指向 brickery-workbench 仓库 Release（旧 brickery 仓库链接已清零，commit e18b701）
- 下载入口（镜像加速）：`https://gh-proxy.com/https://github.com/suipu-boop/brickery-workbench/releases/download/v0.1.0/BrickeryWorkbench-0.1.0.dmg`
- 发布流程（固化）：代码改动 → 跑 `scripts/build_workbench_app.sh`（清旧重建）→ `gh release create`（/opt/homebrew/bin/gh，需写全路径）替换资产 → 网页链接不变自动生效
- 教训：新仓库拆分后必须同步建 Release + 迁移下载链接，否则用户仍下载到旧仓库旧包

## 测试协议 · 闭环快速迭代（2026-08-22 用户确立）

**测试链路**（用户手动操作）：下载工坊 dmg → 打开工坊 App → 选积木组装 → 产出 agent 安装包到桌面 → 安装并测试生成 agent（安装引导 → 聊天 → 积木调用）

**出错处理闭环**（用户确立的标准）：
1. 测试中发现问题 → 定位有错的部件/环节（积木逻辑 / 内核 / 工坊后端 / 前端）
2. **直接修改运行中的 agent** 快速验证修复（不重新走完整打包链路）
3. 修复有效后 → **同步把有错的部件更新回网端库**（brick-vault 积木），push 前需用户确认
4. 涉及工坊/内核代码的根因问题 → 先落盘 specs 供用户审阅，不静默改码

**注意事项**：dmg 下载后若报"已损坏"= macOS Gatekeeper 拦截未签名应用（quarantine），清除即可；工坊市场直连 brick-vault，正常应显示约 17 bricks + 2 engines。

## 测试记录 · 闭环快速迭代（2026-08-23）

**环节结论**：
| 环节 | 结果 | 说明 |
|------|------|------|
| 1 工坊 dmg 下载 | 通过 | v0.1.0（104202650 字节），Gatekeeper 拦截已清除 quarantine |
| 2 打开工坊 App | 通过 | 工作台 8765 正常 |
| 3 组装产出安装包 | 通过 | 产出 shadelingmac-0.1.0.dmg 到桌面（68459698 字节） |
| 4a 引导安装 | 通过 | 引导安装程序正常 |
| 4b 聊天界面 | 修复后通过 | 初始卡"检测中"（两个 bug，见下） |
| 4c 积木调用 | 待测 | 工具 17 个就绪，待用户继续测试 |

**已修复部件清单**（运行中 agent 桌面副本验证有效，待同步网端库）：
1. 内核 `setup_wizard.py`：引导页保存配置只写顶层 engine、不同步 active profile → ipc/status 检测读空配置（Bug A）
2. 内核 `chat_ui.py` L1909：JS 字符串裸 `\n` 致整段脚本语法错误不执行，界面永远"检测中"（Bug B）
- spec：`specs/2026-08-23-agent-chat-stuck-fix.md`（待审阅后同步 GitHub）

**测试中配置变更**：补全 `~/Library/Application Support/shadelingmac/config.json` 的 profiles[default] API 配置（与顶层 engine 一致，已备份 .bak-211539）。

### 全功能测试记录（2026-08-23 晚，运行中 agent）

**第一轮 · 只读验证（接口层+UI 层）— 全部通过**
- IPC 只读 15 项全过：health/status/session_list/skill_list/tool_list(17)/models_list/config_get/backup_list/rules_list/task_list/vault_list/mcp_list/drawer_list/core_get/interoception_state
- UI 6 项全过：页面加载（侧栏13导航）、新建会话、设置5tab渲染、引擎状态+医生自检9项全过、备份空列表正常、积木市场加载（已装0/共6块）
- 截图：output/01~11_*.png（conv_1a02891bf07 会话 output 目录）
- 观察点：模型预设下拉仅"默认"1 项；市场 6 块积木待安装（技能库当前为空，装后才可用）；MCP 未配置；会话/备份/记忆库初始为空
- 待办：第二轮写操作测试（安装积木、备份、会话增删改、配置保存），真实对话测试（耗 token，最后做）

## 待办（后续推进项）

- **Developer ID 签名 + 公证（notarize）**：2026-08-23 用户拍板走合法开发者路子，根治 Gatekeeper"已损坏"拦截（当前未签名，每次下载需清 quarantine/右键打开）。路径：Apple Developer 账号 → Developer ID Application 证书 → 构建脚本加 codesign（--options runtime）+ xcrun notarytool submit + stapler staple → 产物签名+公证，用户下载直接双击打开。涉及构建脚本改动，需先落盘 specs 供审阅
- **site GitHub Pages 迁移**：brickery-workbench 仓库启用 Pages（原 suipu-boop.github.io/brickery 域名，目标 suipu-boop.github.io/brickery-workbench），当前 site/ 已在新仓库但 Pages 未启用
- 工坊仓库 ROADMAP 建置
- 原 brickery 仓库旧 Release dmg（104193138）是否移除（避免混淆，可选）
- 进度积木细化设计（specs/idea-progress-brick.md，已迁至工坊仓库）
