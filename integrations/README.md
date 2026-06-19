# integrations/ — OneKey → n8n → Claude routine bridge

OneKey's native "fire a webhook to Claude" path wasn't triggering runs. This n8n workflow bridges
it: **OneKey note → n8n webhook → n8n calls the Claude `OneKey – Per-note` routine's *fire* API.**

## File
- `n8n-onekey-to-claude.json` — importable n8n workflow. Two nodes:
  1. **OneKey Webhook** — receives the POST from OneKey (`POST`, path `/onekey-note`, replies `200`).
     Its production URL is what you put into OneKey.
  2. **Trigger Claude Routine** — POSTs to the routine **fire** endpoint with the required auth +
     headers, sending `{"text": "<the note>"}`. The note is pulled from OneKey's payload at
     `body.transcription.enhancedText` (fallback `body.transcription.text`, then generic fields).

## How the Claude routine fire API works (why the first try 401'd)
Triggering a routine is **not** a plain webhook and **not** a Claude API key. Per the
[fire-a-routine docs](https://platform.claude.com/docs/en/api/claude-code/routines-fire) you must:
- Call `POST https://api.anthropic.com/v1/claude_code/routines/{trig_…}/fire`
- Send `Authorization: Bearer <per-routine token>` (token looks like `sk-ant-oat01-…`)
- Send `anthropic-beta: experimental-cc-routine-2026-04-01` (mandatory) and `anthropic-version: 2023-06-01`
- Send a body with a **`text`** field only (other fields are ignored). The `text` is handed to the
  routine alongside its saved prompt.

## Setup (one time)
1. **Get the routine's API trigger token + URL.** At **claude.ai/code/routines**, open the
   `OneKey – Per-note` routine → **Add another trigger** → choose **API** → **Generate token**.
   Copy BOTH the **token** (`sk-ant-oat01-…`, shown once — save it now) and the **full `…/fire` URL**.
   *(This is a different trigger type than "Webhook". The fire API needs the API trigger.)*
2. **Import** the workflow into n8n: *Workflows → ⋯ → Import from File →* `n8n-onekey-to-claude.json`.
3. Open the **Trigger Claude Routine** node:
   - **URL:** replace `PASTE_ROUTINE_FIRE_URL_HERE_ENDING_IN_/fire` with the full fire URL (must end `/fire`).
   - **Headers → Authorization:** replace `PASTE_ROUTINE_API_TRIGGER_TOKEN_HERE_sk-ant-oat01-xxx`
     with `Bearer sk-ant-oat01-…` (keep the word `Bearer ` in front). The other two headers are
     already filled in.
     *(More secure option: instead of a plain header, use Authentication → Generic Credential Type →
     Header Auth, name `Authorization`, value `Bearer sk-ant-oat01-…`.)*
4. **Activate** the workflow (top-right toggle). The webhook is only live when active.
5. Open the **OneKey Webhook** node → copy its **Production URL** → paste into **OneKey's** webhook setting.

## Test
- In the **Trigger Claude Routine** node, click **Execute step** → expect **200** with a
  `claude_code_session_id` (that means the routine started). A 401 = bad/missing token; a 400 =
  missing `anthropic-beta` header or the routine is paused.
- Then take a note in OneKey → the chain runs end-to-end; the Claude routine writes a row to the
  OneKey Progress Log.

## Notes
- n8n must be reachable from OneKey. n8n Cloud is fine; local/self-hosted needs a public URL/tunnel.
- Each fire call starts a **new** Claude Code session and counts against your routine run allowance.
- The routine receives the note as freeform `text`; its prompt (see `../ROUTINES.md`, Routine 1)
  treats that as the incoming note.
