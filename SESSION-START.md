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

## 四仓库同步状态（2026-08-22）

| 仓库 | 本地路径 | GitHub | 状态 |
|------|---------|--------|------|
| 积木工坊 | /Users/suipu/Dev/brickery-workbench | suipu-boop/brickery-workbench | 已同步 |
| 生成 agent | /Users/suipu/Dev/brickery | suipu-boop/brickery | 已同步 |
| 积木加工厂 | /Users/suipu/Dev/brick-vault | suipu-boop/brick-vault | 已同步 |
| 架构总览 | /Users/suipu/Dev/brickery-meta | suipu-boop/brickery-meta | 本文件所在 |

## 待办（后续推进项）

- site 网页站 GitHub Pages 迁移到 brickery-workbench 仓库
- 工坊仓库 ROADMAP 建置
- 原 brickery 仓库历史中工坊文件的清理归档（可选）
- 进度积木细化设计（specs/idea-progress-brick.md，已迁至工坊仓库）
