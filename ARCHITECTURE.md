# Brickery 三项目架构总览（ARCHITECTURE）

> 本文件是**三个项目共用的顶层导航**。任何会话启动时先读本文件，即可恢复全局视野。
> 维护：brickery-meta 仓库。三项目边界/联系以此为准。

## 一、三项目总览

| 项目 | 仓库 | 定位 | 主要代码 | 典型会话 |
|------|------|------|---------|---------|
| **积木工坊** | [brickery-workbench](https://github.com/suipu-boop/brickery-workbench) | 面向用户的组装+分发端 | app/（Swift 壳）、web/（前端）、site/（网页站）、brickery/web/（工坊后端） | 工坊 UI、打包发布、网页下载站 |
| **生成 agent** | [brickery](https://github.com/suipu-boop/brickery) | agent 底座+产出链路 | brickery/ 内核、runtime/、produce 链路、装配/引导/聊天 | 内核、底座、产出 agent |
| **积木加工厂** | [brick-vault](https://github.com/suipu-boop/brick-vault) | 积木生产端 | 积木库、brick.json 契约、验收闸门 | 积木制作、积木发布 |

## 二、依赖方向（单向）

```
积木加工厂(brick-vault) ──积木(brick.json/.brick)──▶ 积木工坊(brickery-workbench) ──组装+产出──▶ 生成 agent(brickery)
```

- 加工厂产积木 → 工坊浏览选择 → 生成 agent 装配进底座 → 产出 agent 运行积木
- 反向不依赖：工坊/生成 agent 的改动不影响积木库内容

## 三、接口契约（跨项目联结点）

| 契约 | 定义方 | 消费方 | 说明 |
|------|--------|--------|------|
| brick.json | brick-vault | 工坊/生成 agent | 积木清单元数据（5 字段契约，见 brick-vault specs/brick-schema.md） |
| .brick 安装包 | brick-vault | 生成 agent | 自包含积木包，打包/导入链路 |
| 积木市场清单 | brick-vault（raw/API） | 工坊 live_vault | 工坊直连 brick-vault 的 skills/index.json |
| 组装/产出接口 | 生成 agent（assembler/produce） | 工坊后端 | 工坊 server.py 依赖内核 assembler/produce/package |

## 四、内核与共享组件（重点）

- **内核单权威**：brickery 仓库是唯一内核源（brickery/ 包本体）。工坊构建时从 GitHub 拉取内核，合并自己的 brickery/web/ 覆盖（见 brickery-workbench/scripts/build_workbench_app.sh）
- **app/（Swift 壳）**：两项目共享组件，**双仓库各存一份**（brickery-workbench/app、brickery/app），改动需同步，禁止单边修改
- **内嵌 python**：构建产物不入库（.gitignore），两项目各自构建时准备 temp/python

## 五、会话启动协议（每个会话都能了解联系）

1. 新会话开始 → 先读本文件（ARCHITECTURE.md）恢复全局视野
2. 再进本项目 README + ROADMAP（今日进度）+ specs/（设计方案）
3. 跨项目问题 → 查"接口契约"表定位到契约方仓库
4. 涉及共享组件（app/、内核）改动 → 提示需同步另一仓库

## 六、本地目录结构

```
/Users/suipu/Dev/
  brickery-workbench/   # 积木工坊
  brickery/             # 生成 agent
  brick-vault/          # 积木加工厂（积木库）
  brickery-meta/        # 顶层导航（本文件）
```

## 七、变更记录

- 2026-08-22：三项目拆分拍板（方案 A），本文件创建。规划见 brickery-workbench/specs/project-org.md
