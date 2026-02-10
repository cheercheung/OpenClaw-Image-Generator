# Evolink Z-Image Skill (OpenClaw)

Codex skill to generate images via the Evolink `z-image-turbo` API (submit → poll → download `.webp`).

- 中文说明：see `README.zh-CN.md`

## Install

```bash
npx skills add ./skills/evolink-z-image
```

## API key

Set `EVOLINK_API_KEY` (recommended) so you don’t need to paste it every time:

- macOS (zsh): `echo 'export EVOLINK_API_KEY="...your key..."' >> ~/.zshrc && source ~/.zshrc`
- Linux (bash): `echo 'export EVOLINK_API_KEY="...your key..."' >> ~/.bashrc && source ~/.bashrc`
- Windows (PowerShell): `[Environment]::SetEnvironmentVariable("EVOLINK_API_KEY","...your key...","User")`

If it’s not set, the agent should ask you for the key on first use.

## Use

In chat (after you provide/configure the API key), just say:

- `生图：<your prompt>` (Chinese)
- `generate a picture: <your prompt>` (English)

Examples:

- `生图：一只可爱的猫咪`
- `generate a picture: a cute cat`

Defaults:

- `size`: `1:1`
- `nsfw_check`: `false`

The agent should return the image URL and also download the image to a local `.webp` file (URL expires in ~72 hours).

## OpenClaw delivery (required)

This skill is intended for OpenClaw. After downloading the `.webp` locally:

- `read` only previews the image in this chat (not delivered to the user).
- The agent must call the OpenClaw messaging action and send the image using the message `filePath` parameter that points to the downloaded `.webp` (do not only paste the URL).
