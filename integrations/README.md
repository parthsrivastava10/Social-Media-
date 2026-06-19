# integrations/ — OneKey → n8n → Claude bridge

OneKey's native "fire a webhook to Claude" path wasn't triggering runs. This n8n workflow bridges
it: **OneKey note → n8n webhook → n8n POSTs to the Claude `OneKey – Per-note` routine.**

## File
- `n8n-onekey-to-claude.json` — importable n8n workflow. Two nodes:
  1. **OneKey Webhook** — receives the POST from OneKey (`POST`, path `/onekey-note`, replies `200`
     immediately). Its production URL is what you put into OneKey.
  2. **Trigger Claude Routine** — POSTs to the Claude routine's webhook URL, forwarding a normalized
     `{ "note": "...", "source": "onekey-via-n8n" }`. The `note` is pulled from the incoming OneKey
     payload (tries `note` / `text` / `transcript` / `content` / `message`, else the whole body),
     so the Claude routine's payload parser always finds the text.

## Setup (one time)
1. **Import** into n8n: *Workflows → ⋯ menu → Import from File →* select `n8n-onekey-to-claude.json`.
2. Open the **Trigger Claude Routine** node → replace `PASTE_CLAUDE_PER_NOTE_ROUTINE_WEBHOOK_URL_HERE`
   in the **URL** field with your Claude `OneKey – Per-note` routine's webhook URL → save.
3. **Activate** the workflow (top-right toggle). The webhook is only live when the workflow is active.
4. In the **OneKey Webhook** node, copy the **Production URL** → paste it into **OneKey's** webhook setting.

## Test
- In n8n, open the **OneKey Webhook** node → **Listen for test event**, then POST to the test URL
  (or take a note in OneKey). You should see the execution run green, and the Claude routine should
  show a run + write a row to the OneKey Progress Log.
- If the Claude call fails, check the **Trigger Claude Routine** node's URL and that the Claude
  routine is enabled.

## Notes
- The Claude routine prompt (see `../ROUTINES.md`, Routine 1) already reads `note` from the body, so
  the normalized payload above drops straight in.
- If OneKey sends a non-JSON body, the expression falls back to stringifying whatever arrived — the
  routine still receives the text under `note`.
