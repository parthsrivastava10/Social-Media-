---
name: calendar-logic
description: >-
  The weekly-slot model and reprioritization math for OneKey's Instagram content calendar. Use
  whenever a reel must be inserted, moved, or reprioritized: 7 reels/week (Mon–Sun), insert a new
  reel at the soonest UNFILMED slot, push the displaced reel to the next open slot, cascade
  collisions forward, never delete, and respect the Saturday freeze line. Invoked by the
  social-media-manager when building replacement plans or the Saturday draft of next week's slate.
---

# Calendar Logic — weekly slots, freeze line, bump & cascade

This skill defines how reels are scheduled and reprioritized so the calendar always holds **7
ready-to-film reels per week** and never violates the **Saturday freeze line**. It is pure
scheduling logic — pillar/strategy decisions come from `context/brand-strategy.md`.

## 1. The weekly-slot model

- A **week** has **7 slots**, one per day, **Monday → Sunday** (one reel posted per day).
- Each slot holds: **Pillar · Concept · Status · Scheduled Date · Proposed Date · Filmed?**
- **Status** values: `Proposed` → `Approved` → `Filmed` → `Posted`.
- **Default pillar mix per week: 2/2/2/1** — 2× 30 Founders, 2× Voice/POV, 2× Pain, 1× Product.
- A slot is **UNFILMED** if `Filmed? = false` (Status not yet `Filmed`/`Posted`).

## 2. The production rhythm these slots serve (rolling weekly cycle)

- **Friday:** audit the currently-posting week (Windsor) → finalize the **about-to-shoot** week.
- **Saturday:** **film all 7 reels** of the finalized week (locks the week) → draft the *following*
  week as `Proposed`.
- **Sunday:** Parth is notified the **next week's calendar** (drafted Saturday) is ready to **review
  in Notion**.
- **Mon–Sun:** post one reel/day (manual).
- **Mid-week (any day):** OneKey-webhook notes can update the upcoming week (see the Friday cutoff in §3).
- So at any time there are typically two weeks in view: the **current filmed week** (locked, posting)
  and the **next unfilmed week** (the imminent Saturday batch — drafted last Saturday, reviewed
  Sunday, finalized at Friday's audit, still editable until then).

## 3. The FREEZE LINE + the Friday cutoff (timing constraints)

- **Freeze line:** once a week is **filmed** (Saturday), every slot in it is **locked**. Do not
  insert, replace, reorder, or delete reels in a filmed week. Agents never set `Status = Filmed` —
  only Parth's Saturday shoot does.
- Any change targets the **soonest UNFILMED week**. **Determining the target week:** find the
  earliest week where the slots are not yet `Filmed` / whose Saturday film session hasn't happened.
- **Friday cutoff (practical):** the upcoming **Saturday batch** can still be changed by a note that
  arrives **before Friday's audit**. From **Friday onward**, that batch is settled by the audit and
  about to be filmed — route the change to the **week after** instead. (The freeze is the hard lock at Saturday; Friday is
  the soft cutoff that protects the imminent shoot.)

## 4. Insert + bump + cascade algorithm

When a new reel **N** must be added to the soonest unfilmed week **W**:

1. **Choose the target slot** in W — the soonest UNFILMED day that best fits N's priority (a
   time-sensitive update goes as early as possible; otherwise the soonest open/lowest-priority slot).
2. **Insert N** at that slot with `Status = Proposed` and a `Proposed Date`.
3. **Displace, don't delete.** The reel **D** that occupied the slot moves to the **next open slot**
   (the soonest later unfilmed slot in W that is empty).
4. **Cascade collisions forward.** If the next slot is occupied, that reel shifts forward too, and so
   on down the week. Reels only ever move **later**, never into a filmed slot, never off the cliff.
5. **Overflow → next week.** If the cascade runs past Sunday of W, the overflowed reel rolls to the
   **soonest open slot of the following (still unfilmed) week**. It is **never deleted**.
6. **Never break the freeze:** if any step would land a reel in a filmed week or filmed slot, skip
   that slot and continue cascading into unfilmed territory only.

## 5. Mix preservation & rebalancing

- Prefer to **replace a reel of the same pillar** so the 2/2/2/1 mix holds within W.
- If a high-priority update forces an off-mix week (e.g. tilting to 2 Product for a redesign), that is
  allowed **for that week** — explicitly note it and **rebalance in the following week** (e.g. drop
  Product back to 1, restore Pain to 2). Carry displaced reels forward rather than dropping them.
- Always end with **exactly 7 filled slots** in each affected unfilmed week.

## 6. Status discipline (proposals are not live)

- New/moved reels are `Status = Proposed` with a `Proposed Date` until Parth approves.
- The social-media-manager **proposes**; only after approval does it set `Approved` and write the
  real `Scheduled Date`. Nothing is `Filmed` until the Saturday shoot.

## 7. Worked example (the redesign integration)

Week W (unfilmed, default mix 2 Founders / 2 POV / 2 Pain / 1 Product). Three update-driven reels
must enter: **"We deleted features" (POV)**, **new recording animation (Product)**, **before/after
reveal (Product)**.

1. Insert the three at the soonest fitting unfilmed slots (e.g. Wed/Thu/Sat).
2. The displaced reels bump forward; one Pain reel ("12 follow-ups → 2 survive") cascades past Sunday
   and **overflows into the next unfilmed week** (never deleted).
3. W now tilts to **2 Founders / 2 POV / 2 Product / 1 Pain** — an intentional, noted off-mix week to
   spend the redesign while fresh.
4. The **following week rebalances**: Product → 1, Pain → 2, and the carried-over follow-ups reel lands
   in an open slot. Both weeks end with exactly 7 reels. The freeze line was respected throughout
   (only unfilmed weeks were touched).
