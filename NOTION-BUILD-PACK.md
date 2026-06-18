# NOTION-BUILD-PACK.md — paste-ready Notion setup

> Use this to build the live Notion state by hand (≈10 min). It complements `SETUP.md` (the
> schemas) with the **exact row values** for the week currently in your "🎬 1Key Content Calendar
> Plan" page. The full reel scripts already live in that page's toggles — copy them into the new
> rows' bodies.
>
> **Why manual:** the remote/async Claude session cannot clear the Notion MCP **write** approval
> (reads work, writes don't). To have it built automatically instead, re-run the build request from
> the **desktop or web Claude app**, where the "Allow" prompt appears inline.

---

## 1) Content Calendar — database

On the "1Key Content Calendar Plan" page: `/database` → **Database - Inline** → name **Content
Calendar**. Properties:

| Property | Type | Options |
|---|---|---|
| **Reel** | Title | — |
| **Week** | Select | `Wk of Jun 22` |
| **Day** | Select | Mon, Tue, Wed, Thu, Fri, Sat, Sun |
| **Scheduled Date** | Date | — |
| **Proposed Date** | Date | — |
| **Status** | Select | `Proposed` (yellow), `Approved` (blue), `Filmed` (orange), `Posted` (green) |
| **Pillar** | Select | `30 Founders` (purple), `Voice of OneKey (POV)` (blue), `Pain` (orange), `Product / Activation` (green) |
| **Concept** | Text | — |
| **Caption** | Text | — |
| **Hashtags** | Text | — |
| **Filmed?** | Checkbox | — |

Add **7 rows** (all `Week = Wk of Jun 22`, all `Status = Approved`, all `Filmed? = unchecked` —
you check Filmed? on Sat Jun 20 after the shoot, which trips the freeze line). Copy each reel's
hook/script/CTA from the existing page toggles into the row body, and its Caption + Hashtags into
those fields.

| Reel | Day | Scheduled Date | Pillar |
|---|---|---|---|
| I asked 30 founders 1 question | Mon | 2026-06-22 | 30 Founders |
| 2am, brain won't shut up | Tue | 2026-06-23 | Pain |
| We deleted features | Wed | 2026-06-24 | Voice of OneKey (POV) |
| New recording animation | Thu | 2026-06-25 | Product / Activation |
| Ideas you can't act on yet | Fri | 2026-06-26 | 30 Founders |
| Before/after redesign reveal | Sat | 2026-06-27 | Product / Activation |
| "Just write it down" is bad advice | Sun | 2026-06-28 | Voice of OneKey (POV) |

**Views to add:** Calendar (by `Scheduled Date`) · Board (group by `Status`) · Table (group by `Week`).

> This week posts Jun 22–28 and is filmed Sat Jun 20. From here the rolling cycle takes over: each
> Saturday's run drafts the *next* week as `Proposed` for that Monday's review.

## 2) OneKey Progress Log — database

New inline database **OneKey Progress Log**. Properties: **Title** (title) · **Date** (date) ·
**Source note** (text) · **Category** (select: product update / feature ship / design change / user
feedback / business / idea) · **Distilled update** (text) · **Content angle** (text). View: table
sorted by Date (newest first). Leave empty — the context-manager appends to it. **Append-only.**

## 3) Weekly Audit — page

New sub-page **Weekly Audit**. Per-week template:

```
## Week of ____
**Metrics pulled** (Windsor `instagram_public` + any Instagram Insights you supply)
**What's working / not** vs last week
**Next week's pillars**
**Next-week 7-reel slate**  — table: Day · Pillar · Concept · Status
```

## 4) Wire it up

Paste each object's URL into your routine config (see `ROUTINES.md` → Shared config): the Content
Calendar DB, the Progress Log DB, the Weekly Audit page, plus the Windsor connector and Slack channel.
