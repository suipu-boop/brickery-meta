---
AIGC:
    Label: "1"
    ContentProducer: 001191440300708461136T1XGW3
    ProduceID: 1ff3ab34626ddcd667748776b4e29487_5311be569ef511f1a65b525400826444
    ReservedCode1: 4z/NE5cbkXPTYXfaBIqLqNNIr/UAWlWo0sSvUYOyy5JiQ9hHWJGzZHgepk8dmnv+lZ87KATaA1ovUVqD4W4ZvbtorVvR5DekoEGF5+gFwOO55Op4TeagJEp/X6FMSEfYtJJ2g4kxUPycxRotpyFZEINGS6eVC5j33+x3ArrLXoAvLjsKJIfK6DgRFcY=
    ContentPropagator: 001191440300708461136T1XGW3
    PropagateID: 1ff3ab34626ddcd667748776b4e29487_5311be569ef511f1a65b525400826444
    ReservedCode2: 4z/NE5cbkXPTYXfaBIqLqNNIr/UAWlWo0sSvUYOyy5JiQ9hHWJGzZHgepk8dmnv+lZ87KATaA1ovUVqD4W4ZvbtorVvR5DekoEGF5+gFwOO55Op4TeagJEp/X6FMSEfYtJJ2g4kxUPycxRotpyFZEINGS6eVC5j33+x3ArrLXoAvLjsKJIfK6DgRFcY=
---

# Spec · 生成 agent 聊天界面卡"检测"修复（2026-08-23）

> 来源：积木工坊全链路测试（闭环快速迭代）· 环节 4（安装引导 → 聊天界面）
> 状态：已定位根因 + 已在运行中 agent（桌面副本）验证修复，**待用户审阅后同步网端库**

## 一、现象

工坊组装产出的 agent（shadelingmac-0.1.0）安装引导完成后，跳转聊天界面（18767），
顶栏一直显示「daemon 检测中... / 引擎检测中...」，加载不出任何东西。

## 二、根因（两个独立 bug 叠加）

### Bug A · 引导页配置保存不同步 active profile

- **部件**：内核 `brickery/runtime/setup_wizard.py` → `_save_config()`
- **问题**：保存 API 配置时只写 `cfg.engine`（顶层 engine 字段），
  **没有同步写入 `profiles[active_profile_id]`**（default 为空）。
- **影响**：`ipc.py` 构建引擎 / `status` 检测读的是 active profile（空），
  返回 `network_configured=false`、`api_model=""` → 聊天界面引擎检测不过。
- **证据**：config.json 顶层 engine 有 api_url/api_key（火山引擎 ark），
  profiles[default] 全空；手动补齐 profiles 并重启后 `network_configured=true`。

### Bug B · chat_ui.py JS 字符串裸 `\n` 导致整段脚本不执行

- **部件**：内核 `brickery/runtime/chat_ui.py` 第 1909 行（PAGE_HTML 内联 JS）
- **问题**：`alert("一键备份完成：\n" + ...)` 中 `\n` 是 Python 转义，
  输出到页面时变成**真实换行** → JS 字符串断裂 → `SyntaxError` →
  **整段 `<script>` 不执行** → 检测逻辑（loadStatus）从未运行，
  页面永远停留在初始文案「检测中...」。
- **范围**：全文件扫描，仅此一处裸 `\n`，其余均为正确 `\\n`。

## 三、修复方案（已在桌面副本验证）

### 修复 1（Bug A · 引导页）
`setup_wizard._save_config` 保存时同步写 active profile：
```python
eng = cfg.engine
eng.api_url = data.get("api_url", eng.api_url)
eng.api_key = data.get("api_key", eng.api_key)
eng.api_model = data.get("api_model", eng.api_model)
eng.api_name = data.get("api_name", eng.api_name)
# 新增：同步激活 profile，保证 ipc/status 检测一致
for p in (cfg.profiles or []):
    if p.get("id") == cfg.active_profile_id:
        p["api_url"] = eng.api_url
        p["api_key"] = eng.api_key
        p["api_model"] = eng.api_model
        p["api_name"] = eng.api_name
```
（运行时兜底：本次测试通过手动补全 config.json 的 profiles[default] 验证。）

### 修复 2（Bug B · JS 转义）
`chat_ui.py` 第 1909 行：
```diff
-  try { const r = await ipc("backup_default", {}); alert("一键备份完成：\n" + (r.dest || r.detail || "已保存到默认备份目录")); }
+  try { const r = await ipc("backup_default", {}); alert("一键备份完成：\\n" + (r.dest || r.detail || "已保存到默认备份目录")); }
```

## 四、验证结果（运行中 agent 桌面副本）

| 检查项 | 结果 |
|--------|------|
| 页面内联 JS `node --check` | OK（修复前 SyntaxError @L1397） |
| IPC status `network_configured` | true（修复前 false） |
| 引擎识别 | ark-code-latest / Huoshan Coding Plan |
| daemon | running |
| 聊天界面 | 用户确认已正常进入 |

## 五、待办（需用户确认后执行）

1. 把修复同步到**内核网端库**（brickery 仓库）：
   - `brickery/runtime/setup_wizard.py`（Bug A）
   - `brickery/runtime/chat_ui.py`（Bug B）
2. 同步到工坊构建拉取内核链路（工坊重新打包时自动包含修复）
3. 可选：后续新版产出 agent 时回归验证引导→聊天全链路

## 六、备注

- 本次为运行中 agent 快速验证修复，未走完整打包链路；
  桌面副本 `/Users/suipu/Desktop/shadelingmac/` 已含修复。
- 涉及内核代码，按约定先落盘本 spec，审阅确认后再改码/push。
*（内容由AI生成，仅供参考）*
