# Qwen3-Omni-Flash 备用配置

当 MiMo 不可用或需要切回 Qwen 视觉模型时，执行：

```bash
hermes config set auxiliary.vision.provider dashscope
hermes config set auxiliary.vision.model qwen3-omni-flash
hermes config set auxiliary.vision.base_url https://dashscope.aliyuncs.com/compatible-mode/v1
hermes config set auxiliary.vision.api_key <从 .env 获取>
```

切回 MiMo：
```bash
hermes config set auxiliary.vision.provider mimo
hermes config set auxiliary.vision.model mimo-v2.5
hermes config set auxiliary.vision.base_url https://api.xiaomimimo.com/v1
hermes config set auxiliary.vision.api_key <从 .env 获取>
```

对比：

| | Qwen3-Omni-Flash | MiMo-V2.5 |
|---|---|---|
| 速度 | ~6.5s | ~9.5s |
| 输入 ¥/M | 3.3 | 1.01 |
| 输出 ¥/M | 6.9 | 2.03 |
| 模态 | 文+图+视频+音频 | 文+图+视频+语音 |
