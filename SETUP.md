# SETUP.md — What YOU create in Notion (one-time, by hand)

> This repo defines agent **behavior**. Notion holds the living **state** the agents read and
> write at runtime. This file tells **you (Parth)** what to build in Notion before the routines
> run. **The agents do NOT create these for you, and nothing in this session writes to your
> Notion** — create them yourself, then paste their URLs/IDs into your routine config when you
> wire up the automation later.
>
> You already have a **"1Key Content Calendar Plan"** Notion page. The recommended next step is to
> turn the calendar into a proper **database** (one row per reel) so multi-week tracking, the
> freeze line, and reprioritization work cleanly. Schemas below.

---

## 1. Content Calendar — database

**Type:** Notion database (table). One **row per reel**. This is the heart of the system — the
7-reel weekly slate the social-media-manager proposes against and the script-writer fills.

**Properties:**

| Property | Type | Notes |
|---|---|---|
| **Name / Title** | Title | Short reel title (e.g. "We deleted features"). |
| **Week** | Select or Number | Which production week (e.g. "Wk of Jun 22" or a week number). |
| **Day** | Select | Mon / Tue / Wed / Thu / Fri / Sat / Sun. |
| **Scheduled Date** | Date | The post date once approved (Mon–Sun). |
| **Proposed Date** | Date | Used while a reel is `Proposed` and not yet approved. |
| **Status** | Select | `Proposed` → `Approved` → `Filmed` → `Posted`. |
| **Pillar** | Select | `30 Founders` / `Voice of OneKey (POV)` / `Pain` / `Product / Activation`. |
| **Concept** | Text | The finalized concept (hook angle, core idea, follow-reason). |
| **Script** | Text / Page | The full reel script (the script-writer's draft). |
| **Caption** | Text | The post caption (zero typos). |
| **Hashtags** | Text / Multi-select | The per-reel hashtag set. |
| **Filmed?** | Checkbox | Drives the **freeze line** — checked = locked, do not reprioritize. |

**Recommended views:**
- **Calendar view** on `Scheduled Date` (and a second on `Proposed Date`) — see the week at a glance.
- **Board view** grouped by **Status** — watch reels move Proposed → Approved → Filmed → Posted.
- **Board/Table** grouped by **Week** — confirm each week holds exactly 7 reels.

> **Why `Filmed?` matters:** the calendar-logic skill never touches a reel/week where `Filmed? = true`.
> Keep this honest — check it on the Saturday you actually film, and the freeze line takes care of itself.

## 2. OneKey Progress Log — database

**Type:** Notion database (table). **Append-only**, written by the **context-manager** whenever a
new note arrives. One **row per distilled update**.

**Properties:**

| Property | Type | Notes |
|---|---|---|
| **Title** | Title | Short label for the update + date. |
| **Date** | Date | When the note was processed. |
| **Source note** | Text | The raw note (lightly cleaned). |
| **Category** | Select | product update / feature ship / design change / user feedback / business / idea. |
| **Distilled update** | Text | 2–5 crisp bullets: what changed, why it matters. |
| **Content angle** | Text | Optional reel-angle suggestion for the social-media-manager. |

**View:** Table sorted by **Date** (newest first). Never overwrite old rows — this is the product's
running history.

## 3. Weekly Audit — page

**Type:** Notion page (or a database of weekly entries if you prefer history). Written by the
**social-media-manager** during the **Sunday** job. Each weekly audit should capture:

- **What's working / not working vs last week** — pillars/formats/hooks that traveled,
  follow-conversion signal, cadence adherence.
- **Metrics pulled** — from Windsor.ai (`instagram_public`) plus any Instagram Insights you supply
  (reach, saves, profile visits, link taps — the public connector can't see these).
- **Next week's pillars** — the proposed emphasis.
- **The next-week 7-reel slate** — Day / Pillar / Concept / Status (ready to shoot Saturday).

## 4. The triggers & notifications to wire up (when you build the routines)

These map to the three orchestration flows in `CLAUDE.md`. **None are created in this scaffolding
session** — this is your go-live checklist.

- **OneKey webhook → per-note flow.** Point the OneKey note webhook at the routine so that when you
  record an update mid-week and fire it, the agents receive the context. Rule baked into the system:
  a note **before Friday** can change the imminent **Saturday batch**; **Friday onward** it targets
  the week after.
- **Saturday schedule → audit + shoot day.** A Saturday trigger runs the audit (Windsor) on the
  currently-posting week, finalizes the about-to-film week, and — after you film — drafts the next
  week as `Proposed`.
- **Monday-morning schedule → review notification.** A Monday-morning trigger notifies you (via the
  Slack channel) that **next week's calendar is ready to review in Notion.** You review there and
  tell the agent any changes.

## 5. Connections & bootstrap

1. Copy each database/page URL (and ID) into the routine config — starting with the **Content
   Calendar DB** (convert the existing **"1Key Content Calendar Plan"** page into the database in §1).
2. Connect the **Windsor.ai `instagram_public`** connector for the `onekey_notes` account.
3. Connect the **Slack** channel where review/priority notifications should land.
4. **Bootstrap:** your calendar currently holds **one week** of reels — the batch you film **this
   Saturday**. That week posts the following Mon–Sun. From the first Saturday onward the rolling cycle
   takes over: each Saturday's run drafts the *next* week as `Proposed` for that Monday's review.

> Reminder: creating routines, attaching triggers, and any live Notion/Slack/Windsor writes are
> **out of scope for the scaffolding session** — this file is your manual checklist for when you're
> ready to go live.
