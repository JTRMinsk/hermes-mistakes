# `hermes config set` 不兼容 YAML 列表格式

> 日期：2026-06-29 · 严重度：🔴 严重 · 模型：deepseek-v4-pro

## 场景

用户要添加 SiliconFlow 作为 custom_providers，接入 GLM-5.2。我用 `hermes config set custom_providers.siliconflow.name "SiliconFlow"` 逐条配。

## 错误

`hermes config set custom_providers.siliconflow.name "SiliconFlow"` 把所有配置写成了 YAML dict 结构：

```yaml
custom_providers:
  siliconflow:            # ← dict key，不是列表项
    name: SiliconFlow
```

Hermes 要求的格式是 YAML 列表：

```yaml
custom_providers:
  - name: SiliconFlow     # ← 列表项
    api: ...
```

配置完没跑 `hermes doctor` 验证，直接说"配好了"。用户后来调用时 provider 认证失败，退回 gemma-local。反复修补三轮——`hermes config set` 持续生成嵌套 dict，最后用 python 直接改 yaml 文件才通。

## 实际正确答案

1. `hermes config set` 只支持标量/dict 路径，不支持 YAML 列表追加
2. 凡是 `custom_providers`、`fallback_providers`、`mcp_servers` 这种列表结构，**直接用 python + yaml 库改 config.yaml**
3. 改完立刻跑 `hermes doctor` 或 `hermes chat -q "test" --provider xxx` 验证

## 根因

不了解 Hermes config 的底层 YAML 格式与 `hermes config set` 的能力边界。`config set` 用 `.` 分隔 key 路径，天然生成 dict 结构，无法处理列表追加。该用 python 时用了 CLI。

## 教训

1. **列表结构 → python**：`mcp_servers`、`custom_providers`、`fallback_providers` 都用 python `yaml.safe_load/dump` 编辑
2. **配置后必须验证**：跑 `hermes doctor` 或一行 chat 测试，不准凭 config 文件说"配好了"
3. **不确定格式时先读现有 config**：看一眼其他列表段（如 `fallback_providers`）就知道正确格式
