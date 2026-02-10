# OpenClaw-Image-Generator

A Claude Code skill for generating images via the [Evolink Z-Image-Turbo](https://evolink.ai) API. Cross-platform, zero dependencies — works natively on Windows, macOS, and Linux.

## Features

- Cross-platform: uses `curl` (natively available on all modern OS)
- No shell escaping issues: user prompts are written to file via Write tool
- Auto-download: images saved locally as `evolink-<timestamp>.webp`
- Configurable size, seed, and polling parameters

## Installation

Copy `generate-z-image.md` to your project's Claude Code commands directory:

```bash
# In your project root
mkdir -p .claude/commands
cp generate-z-image.md .claude/commands/
```

## Prerequisites

You need an Evolink API Key. If you don't have one:

1. Register at https://evolink.ai/signup
2. Go to https://evolink.ai/dashboard/keys to create a key

## Usage

In Claude Code, type:

```
/generate-z-image a cat sitting on the moon
```

Or simply `/generate-z-image` and Claude will ask for your prompt.

## Parameters

| Parameter | Required | Default | Description |
|---|---|---|---|
| prompt | Yes | — | Image description |
| size | No | `1:1` | Aspect ratio: `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `9:16`, `16:9`, `1:2`, `2:1`, or custom `WxH` (376-1536px) |
| seed | No | random | Random seed for reproducibility |

## Notes

- Image URLs expire in **72 hours** — images are auto-downloaded locally as `evolink-<timestamp>.webp`
- First-time use will prompt for your API key; set `EVOLINK_API_KEY` as a system environment variable to skip this step