---
name: evolink-z-image
description: Generate images with the Evolink Z-Image-Turbo API (submit task, poll status, download the resulting .webp). Use when a user asks to generate an image via Evolink or mentions z-image-turbo.
---

# Evolink Z-Image (Z-Image-Turbo)

Use this skill when the user wants to generate an image via Evolink's `z-image-turbo` model.

## Inputs to collect

- `prompt` (required; ask if missing; max 2000 chars)
- `size` (optional; default `1:1`; allowed: `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `9:16`, `16:9`, `1:2`, `2:1`, or custom `WxH` with 376–1536 px)
- `nsfw_check` (optional; default `false`)
- `seed` (optional; integer)

## API key

Check `EVOLINK_API_KEY` in the environment. If it is empty/unset, ask the user for their key.

- If they don't have one: direct them to register and create a key in the Evolink dashboard.
- Do not rely on `export`/`set` persisting across separate shell/tool calls; pass the key into the same command you run.

## Preferred execution (Python, no deps)

Prefer running the bundled script (avoids shell escaping issues and brittle JSON parsing):

```bash
python3 scripts/evolink_z_image.py \
  --api-key "$EVOLINK_API_KEY" \
  --prompt "a cat sitting on the moon" \
  --size "1:1" \
  --nsfw-check false
```

If the user provided a `seed`, include `--seed 42`. If they want a specific output filename, include `--out evolink-YYYYmmdd-HHMMSS.webp`.

## Fallback execution (curl + heredoc)

If Python is unavailable, use the curl flow in `references/curl_heredoc.md`.

## Result handling

- On success: report the image URL and the downloaded filename. Remind the user the URL expires in ~72 hours.
- On failure: surface the API error message/body and suggest retrying with a simpler prompt or different size.
- On timeout: provide the task ID for manual follow-up.
