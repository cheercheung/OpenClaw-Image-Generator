# Evolink Z-Image 技能（OpenClaw）

这是一个 生图skill：调用 Evolink（https://evolink.ai） `z-image-turbo` API 生成图片（提交任务 → 轮询 → 下载 `.webp`）。

- English: see `README.md`

## 安装

```bash
npx skills add ./skills/evolink-z-image
```

## 配置 API Key

推荐把 `EVOLINK_API_KEY` 写到系统环境变量里，后续就不用每次粘贴：

- macOS（zsh）：`echo 'export EVOLINK_API_KEY="...你的 key..."' >> ~/.zshrc && source ~/.zshrc`
- Linux（bash）：`echo 'export EVOLINK_API_KEY="...你的 key..."' >> ~/.bashrc && source ~/.bashrc`
- Windows（PowerShell）：`[Environment]::SetEnvironmentVariable("EVOLINK_API_KEY","...你的 key...","User")`

如果没有设置，第一次用的时候 agent 应该会先向你要 key。

## 使用方式

在对话里直接说（先提供/配置好 API Key 后）：

- `生图：<提示词>`

示例：

- `生图：一只可爱的猫咪`
- `生图：赛博朋克城市夜景，霓虹灯，雨天`

默认参数：

- `size`：`1:1`
- `nsfw_check`：`false`

生成完成后，agent 应该返回图片 URL，并且把图片下载到本地 `.webp` 文件（URL 大约 72 小时过期，建议及时下载）。

## OpenClaw 交付（必须）

- `read` 工具只是把图片预览在我们对话里（并不等于发给用户）。
- agent 必须调用 OpenClaw 的 message 发送动作，并且通过 message 的 `filePath` 参数把本地 `.webp` 文件发给用户（不要只发 URL）。
