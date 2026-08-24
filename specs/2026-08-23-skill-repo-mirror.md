# Spec · 积木市场默认源切换 GitHub 加速镜像（2026-08-23）

> 来源：生成 agent 全功能测试第二轮（积木安装失败排查）
> 状态：方案已验证，**待审阅 + 运行中 agent 验证后同步网端库**

## 一、现象

agent 聊天界面「积木市场」6 块积木可正常列出，但安装任意一块均失败：
`二进制下载失败：网络错误：Not Found`。

## 二、根因

- agent 默认技能源：`ipc.py` 的 `DEFAULT_PUBLIC_SKILL_REPO_URL`
  = `https://raw.githubusercontent.com/suipu-boop/shadeling-bricks/main/skills/index.json`
- 用户网络环境下 **raw.githubusercontent.com 不可达**：
  - 直连实测：`HTTP 000`（15s 超时）
  - gh-proxy 镜像实测：`HTTP 200`（~3s）
- 文件本身存在（gh api 确认），`skill_library.py` 的 base 拼接逻辑正确
  （含 2026-08-22 修复）——**非代码逻辑 bug，是外网访问受限**

## 三、方案

把默认技能源切换到 GitHub 加速镜像，前缀透传原 raw 路径：

```python
# ipc.py
DEFAULT_PUBLIC_SKILL_REPO_URL = (
    "https://gh-proxy.com/https://raw.githubusercontent.com/"
    "suipu-boop/shadeling-bricks/main/skills/index.json"
)
```

- `skill_library.py` 的 base 拼接逻辑**无需改动**（urljoin 对镜像 URL 同样成立）
- 已实测镜像 URL 可用：
  - `https://gh-proxy.com/https://raw.githubusercontent.com/suipu-boop/shadeling-bricks/main/skills/index.json` → 200
  - 同前缀下 `skills/high-config-doc/1.0.0.json` → 200
- 备选镜像（若 gh-proxy.com 失效）：`ghfast.top`（实测 200）

## 四、影响与风险

| 项 | 说明 |
|----|------|
| 影响面 | 仅默认技能源地址；列表/安装/升级/卸载逻辑不变 |
| 风险1 | 依赖第三方镜像服务（gh-proxy.com 为免费公共镜像，可能有速率限制/偶发故障） |
| 风险2 | 镜像不可用时应可回退 raw 直连或换备选镜像 |
| 建议 | 后续可考虑多镜像 fallback 或构建期把积木源打进安装包（离线可用） |

## 五、验证步骤

1. 改运行中 agent 桌面副本 `ipc.py` 的 `DEFAULT_PUBLIC_SKILL_REPO_URL`
2. 重启 agent（IPC 无热重载）
3. `skill_library_list` 强制刷新（force=true）→ 安装 1 块积木 → `skill_list` 确认出现
4. 通过后同步回 brickery 内核库 main 分支（push 前用户确认）

## 六、验证结果（2026-08-23 晚）

- **镜像源改动生效**：`skill_library_list(force=true)` 经 gh-proxy 成功拉取 index，6 块积木正常列出
- **但安装仍失败**：`skill_library_install(high-config-doc)` 报「二进制下载失败：网络错误：Not Found」
- **新根因（推翻了此前"纯网络环境问题"的结论）**：
  - 积木包 `high-config-doc/1.0.0.json` 内 `binary_url` = `https://github.com/suipu-boop/shadeling-skills/releases/download/v1.0.0/editor_sdk`
  - **`shadeling-skills` 仓库不存在**（github.com 直连 404，brickery / shadeling-bricks 仓库均 200 可达）
  - 即：二进制从未发布到 GitHub，与网络/镜像无关
- **本地有匹配产物**：`/Users/suipu/Dev/Shadeling/fixtures/skill_repo/bin/editor_sdk`（202541440 字节，sha256 7d55e4c0...bba2 与 brick.json 声明完全一致）

## 七、遗留决策（待用户确认）

- A. 创建 `shadeling-skills` 仓库并把本地 editor_sdk 发布为 v1.0.0 release 资产（正式修复，需用户 GitHub 操作）
- B. 改 brick.json 的 binary_url 指向其它可达托管（仍须上传 193MB）
- C. 本轮测试先用本地源绕过二进制下载，验证积木功能

## 八、最终方案（2026-08-24 已实施并验证通过）

用户拍板：**不新建仓库**，把指向链路改对、需要推送的产物放进现有库。

1. **积木库 `shadeling-bricks`（brick-vault）**
   - 真相：v1.0.0 release **已有 editor_sdk 资产**，只是 brick.json 把仓库名误写为不存在的 `shadeling-skills`
   - 已修：`skills/high-config-doc/1.0.0.json` 的 `binary_url` → `shadeling-bricks/releases/download/v1.0.0/editor_sdk`，commit `46da880` 已推送
2. **内核 `brickery`（main 分支，commit `463fb81`，待 push）**
   - `ipc.py`：`DEFAULT_PUBLIC_SKILL_REPO_URL` 改 gh-proxy 镜像前缀
   - `skill_library.py`：新增 `_mirror_url()`，`_download_skill` / `_download_binary` 对 github.com / raw.githubusercontent.com 自动加镜像前缀（已带镜像前缀则跳过，防双重前缀）
   - 理由：直连 github 大文件不稳定（50MB 测速 109s 断连），镜像 12.7MB/s（193MB 约 15s）
3. **验证结果（运行中 agent，正式版 /Applications/shadelingmac.app）**
   - `skill_library_install(high-config-doc)`：**27s 安装成功**，193MB editor_sdk 落盘完整（sha256 匹配）
   - `skill_list` 显示「高配文档引擎」已注册
   - 运行环境三处已同步：正式版 / 桌面副本 / 内核库 main
4. **待办**
   - push `brickery` main（`463fb81`）→ 用户确认
   - 后续考虑：多镜像 fallback / 构建期把积木源打进安装包（离线可用）
