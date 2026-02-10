Use the Bash tool to generate an image via the Evolink Z-Image-Turbo API.

## Prerequisites — API Key

Before generating, check if the environment variable `EVOLINK_API_KEY` is set:
- Windows: `echo %EVOLINK_API_KEY%`
- Unix: `echo $EVOLINK_API_KEY`

- If it is **empty or not set**, ask the user to provide their Evolink API Key.
- If the user **does not have a key**, tell them to register at: https://evolink.ai/signup , then go to https://evolink.ai/dashboard/keys to create an API Key.
- Once the user provides the key, save it as a variable in your context for use in subsequent steps. Do NOT use `set` or `export` — they will not persist across Bash calls.

## Instructions

The user wants to generate an image. **If the user did not provide a prompt, ask them first before proceeding.**

Extract the following from their request:
- **prompt**: The image description (required — must ask if missing, max 2000 characters)
- **size**: Image aspect ratio (optional, default "1:1"). Options: "1:1", "2:3", "3:2", "3:4", "4:3", "9:16", "16:9", "1:2", "2:1", or custom "WxH" (376-1536px)
- **seed**: Random seed for reproducibility (optional, range: 1-2147483647)
- **nsfw_check**: Enable stricter NSFW content filtering (optional, default false). Ask the user if they want to enable it.

## Execution

Run the following bash script in a **single Bash call**. The Bash tool runs bash on all platforms (including Windows), so no platform-specific scripts are needed.

Replace `<API_KEY>` with the actual key and `<OUTPUT_FILE>` with `evolink-<TIMESTAMP>.webp`.

The user's prompt is embedded in a single-quoted heredoc (EVOLINK_END), which prevents all shell interpretation. You only need to apply **JSON escaping** to the prompt — escape double quotes with a backslash, and escape backslashes by doubling them.

```bash
API_KEY="<API_KEY>"
OUT_FILE="<OUTPUT_FILE>"

# Submit — heredoc passes JSON via stdin, no temp files needed
RESP=$(curl -s -X POST "https://api.evolink.ai/v1/images/generations" \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d @- <<'EVOLINK_END'
{
  "model": "z-image-turbo",
  "prompt": "<USER_PROMPT>",
  "size": "<SIZE>",
  "nsfw_check": <true|false>
}
EVOLINK_END
)
TASK_ID=$(echo "$RESP" | grep -o '"id":"[^"]*"' | head -1 | cut -d'"' -f4)
echo "Task submitted: $TASK_ID"

if [ -z "$TASK_ID" ]; then
  echo "Error: Failed to submit task. Response: $RESP"
  exit 1
fi

# Poll
MAX_RETRIES=200
for i in $(seq 1 $MAX_RETRIES); do
  sleep 10
  TASK=$(curl -s "https://api.evolink.ai/v1/tasks/$TASK_ID" \
    -H "Authorization: Bearer $API_KEY")
  STATUS=$(echo "$TASK" | grep -o '"status":"[^"]*"' | head -1 | cut -d'"' -f4)
  echo "[$i] Status: $STATUS"

  if [ "$STATUS" = "completed" ]; then
    URL=$(echo "$TASK" | grep -o '"results":\["[^"]*"\]' | grep -o 'https://[^"]*')
    echo "Image URL: $URL"
    curl -s -o "$OUT_FILE" "$URL"
    echo "Downloaded to: $OUT_FILE"
    break
  fi
  if [ "$STATUS" = "failed" ]; then
    echo "Generation failed: $TASK"
    break
  fi
done
if [ "$i" -eq "$MAX_RETRIES" ]; then echo "Timed out after max retries."; fi
```

## Result Handling

After completion, respond to the user:

- **completed**: Show the image URL and confirm the file was downloaded as `evolink-<TIMESTAMP>.webp`. Remind them the URL expires in **72 hours**.
- **failed**: Report the error message from the API.
- **timed out** (200 polls): Inform the user and provide the task ID for manual follow-up.