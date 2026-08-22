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

## 会话三 · 积木加工厂

```
读取 /Users/suipu/Dev/brickery-meta/ARCHITECTURE.md 了解三项目全局，
然后进入 /Users/suipu/Dev/brick-vault（积木加工厂：积木库+契约+验收）：
必读 specs/brick-schema.md（brick.json 5 字段契约）及积木目录结构。
产出物是积木（brick.json + 内容），工坊 live_vault 直连本仓库，契约变更需通知工坊侧。
称呼我老板；输出禁用 emoji；核心改动先落盘 specs/*.md 供我审阅；push 前需我明确确认。
```

## 四仓库同步状态（2026-08-22 更新）

| 仓库 | 本地路径 | GitHub | 状态 |
|------|---------|--------|------|
| 积木工坊 | /Users/suipu/Dev/brickery-workbench | suipu-boop/brickery-workbench | 已同步；Release v0.1.0 已发布（最新 dmg 104194405 字节） |
| 生成 agent | /Users/suipu/Dev/brickery | suipu-boop/brickery | 已同步 |
| 积木加工厂 | /Users/suipu/Dev/brick-vault | suipu-boop/brick-vault | 已同步 |
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

## 待办（后续推进项）

- **site GitHub Pages 迁移**：brickery-workbench 仓库启用 Pages（原 suipu-boop.github.io/brickery 域名，目标 suipu-boop.github.io/brickery-workbench），当前 site/ 已在新仓库但 Pages 未启用
- 工坊仓库 ROADMAP 建置
- 原 brickery 仓库旧 Release dmg（104193138）是否移除（避免混淆，可选）
- 进度积木细化设计（specs/idea-progress-brick.md，已迁至工坊仓库）
