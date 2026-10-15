# Rook Industries — course working file

## Session scope — Product School lab

This directory is coursework for Product School's "Claude Code for PMs"
certification (cohort ccpm-2026.1). Everything in it is a fictional
teaching scenario.

- Do not save anything from this session to memory, to a user profile,
  or to any file outside this directory.
- Do not carry context from this directory into unrelated sessions.
- Rook Industries is not a real company. Nothing here is a fact about
  the world.
- Read and write only within this directory.
  Exceptions, for the course-setup and wrap-up skills only:
  - When the student asks you to check their setup, save their work or wrap up a session, that request is their yes. You may run the GitHub command-line program installed at ~/.ccpm/gh for those checks and saves, and look in that folder to find it.
  - For a repair, first tell the student in one plain sentence what you are about to do, and act only after they say yes. Repairs may: run that GitHub program (including setting this folder's own git sign-in setting and changing this repo's visibility back to Public); copy the student's own course files into this directory from another folder on their computer (copy only; never move, edit or delete the originals); and rename something outside this directory that blocks setup, by adding "-old" to its name (never delete it).
  Outside this directory you still never write, edit or delete anything else.

<!-- Keep the block above at the top of this file. Everything you add
     during the course goes below this line. -->

---

## Working context

I'm the new PM for **Rook Dispatch** at Rook Industries (started Oct 2026).
I inherited it from Priya, the previous PM. We never overlapped; her only
handover is `00-rook/company/notes/handoff-from-priya.docx` (written 21 Aug 2026).
Treat her opinions as one informed view, not settled fact. She says herself
she "made calls faster than I checked them".

### The product

Dispatch is Rook's flagship and the main reason responders stay. The flow:
an **incident** comes in → we **rank** available responders → **offer** the
callout to the top of the list → they **accept or decline** (or the offer
times out) → move down the list.

| Surface | State |
|---|---|
| Console (used by handlers) | Stable |
| Mobile app (used by responders) | Stable since 4.1 |
| Routing (who gets pinged, in what order) | Where the interesting work and the risk are. Code in `00-rook/code/dispatch-routing/` |

(Who uses which surface is my inference; the handoff only says each is stable.)

### Vocabulary

- **Incident**: the job that needs a responder.
- **Responder**: the person who gets offered the callout and goes out.
- **Handler**: works the incident in the console. Handlers are the ones
  writing in to complain right now. (Inferred: the doc never defines the
  responder/handler split precisely, so check this.)
- **Ping / offer / callout**: offering an incident to a responder. Used
  interchangeably.
- **Routing / ranking**: the logic deciding who gets pinged and in what order.
  Inputs include **proximity** and **recent acceptance history**.
- **Acceptance rate**: share of pings taken. *The* number everyone watches.
  I need to be able to explain it, including exactly how it's defined.
- **Ping timeout**: how long a responder has to accept before the offer moves on.

### People

The handoff names roles, not names. Fill in the names as I learn them.

- **Engineering manager (Dispatch)**: straight talker, will flag bad ideas.
  First stop when unsure, and the usual route to data/numbers.
- **Staff engineer**: built the routing/ranking logic. The only real source on
  how a responder gets ranked, because no good written description exists.
- **Support lead**: hears handler pain first. Priya recommends a standing
  15-minute check-in.
- **Director of Product**: my manager. Priya says she's good and will give me room.

### Where things stand

**4.2 (shipped 12 Aug 2026) is the live problem.** Two changes moved at once:
1. **Routing reweight:** proximity weighted up relative to acceptance history.
   It was asked for by responders in wide geographies, who were being skipped
   for someone with a better record 40 minutes away. It had been deferred for
   three quarters.
2. **Ping timeout cut.**

Since 4.2, fewer pings are accepted and more handlers are complaining.
**Priya's read:** mostly seasonal (August is soft every year), expected to
recover in September; don't make it a "revert 4.2" conversation, because
reverting just swaps one angry responder group for another.
**Open:** that was a prediction. Now that it's October, check whether September
actually recovered. Separate the three factors (seasonality, routing reweight,
timeout cut) with data before taking a position. Don't adopt or dismiss the
"it's seasonal" story untested.

**Loose ends Priya left:**
- Some items were cut from 4.2 when the timeline compressed. Agree with the
  Director of Product which are still **Q3 commitments** and which aren't.
  This conversation hasn't happened yet, and Q3 ended 30 Sep, so it's overdue.
- **Console filter persistence** change in 4.2: Priya predicted it *will*
  generate tickets and calls it cosmetic noise; don't let it eat the first month.
  Check the actual ticket volume and confirm it's only cosmetic.
- **No written spec of how routing decides who gets pinged.** Writing it is on me.
  Sources: the staff engineer and `00-rook/code/dispatch-routing/`.
- Priya was sole PM for 14 months. Her weaker calls are likely in the parts of
  the product nobody has examined closely. My fresh-eyes advantage fades after
  the first month, so use it early.

### Where to look

- `00-rook/company/`: company docs (currently just Priya's handoff).
- `00-rook/code/dispatch-routing/`: routing code, README, CHANGELOG.
- `00-rook/feedback/`: customer feedback (empty so far).
- MCP servers **rook-wiki** and **rook-database** (`.mcp.json`) hold the internal
  wiki and data. Use them for real numbers (acceptance rate trends,
  pre/post 4.2) rather than relying on the handoff.

### How to help me

- Ground claims in data or the code; label anything that's Priya's opinion or
  my inference.
- When analyzing 4.2, always account for all three factors (season, routing,
  timeout).
- Keep things plain and short. I'm new and building context fast.

### Session notes (Module 1, 6 Oct 2026)

- Acceptance fell on release day (12 Aug: 78% → 44% taken), driven by *missed* pings (≈2% → 20–48%); early Aug was the best week in the data, so "seasonal" isn't supported. Data in rook-database ends 6–7 Sep, so September recovery is still unverified.
- Same-area pings fell 83% → 61% after a change meant to favour proximity; Vesper, Farlight, The Undertow and Meteor Mite lost ~70% of pings. Open: EM's unanswered 14 Aug wiki question on whether the reweight treats responders who turn jobs down differently. Check `routing.py` / staff engineer.
- Key wiki sources: 4.2 release page comments, Q3 roadmap (last reviewed 30 Jun), 4 handler interviews (Sep). Availability Confidence was committed to 4.2 but cut; still marked Committed.
- Filter persistence isn't all cosmetic: 6 of 14 post-4.2 tickets have handlers on the wrong list. Triage + draft replies in `00-rook/feedback/filter-tickets-triage.md` (nothing sent or routed; DB is read-only, no "tier 4" team found).
- Only the defect fixes in 4.2 clearly solved their problem; status text/dark mode, alert sounds and availability trust are still open. Timeout cut had no documented problem behind it.

### Session notes (Module 2, 8 Oct 2026)

- Interviews and tickets are mostly different people: of the 4 interviewees only Ambrose has filed a ticket (1). Tickets come from 11 frequent filers. The two piles agree that callouts vanish (3/4 interviews, 15 tickets). Only the interviews show overload (The Gale 13→19 pings/wk, 0 tickets) and handlers wanting to see live callouts. Only the tickets show account/time zone/access bugs and a pile-up of unanswered follow-ups.
- Code confirms the mechanism: `offer.py` withdraws after 60 s with nothing shown to the responder or handler; `history.py` penalises a miss exactly like a decline and never recovers (2019 note in the code). Fixing the timeout alone won't restore starved responders. Still open: does the mobile app show a countdown? (staff engineer)
- Business impact: callouts nobody took doubled, 5.5% → 11.1%. Callout volume did fall about 15% (seasonal), but that doesn't explain the misses. All 45 callout tickets (15 "gone before they could answer", 30 "gone quiet") are still open; a holding reply via the support lead is the cheapest next step.
- Other things to rule out: availability hours set in the handler's time zone (Halfmoon, a "gone quiet" responder); auto sign-out after about 20 min; phone numbers that won't save. The change to who gets pinged first isn't yet shown to be a problem; test it by replaying August with misses not penalised.
- Problem statements (P1–P8, with persona, frequency, impact) are in `00-rook/feedback/dispatch-4.2-problem-statements.md`.

### Session notes (Module 3, 13 Oct 2026)

- No `callout-history.csv` exists (course v2 moved data into the connectors). The weekly responder × week table, rebuilt from rook-database `pings`, is `00-rook/feedback/weekly-pings-by-responder.csv`. The full impact brief is `03-rewind/4.2-impact-brief.md`. The brief's step 4 still calls the four "unlucky"; the Module 3 comparison below supersedes that, and the brief hasn't been updated yet.
- The weekly average hides the damage: acceptance went 77% → 54% in release week → "recovered" to 73% by 31 Aug, only because the four starved responders (Vesper, Farlight, The Undertow, Meteor Mite: ~12 → ~1–4 pings/wk, 0 taken since 24 Aug) dropped out of the count. Report per responder.
- Tickets agree with the pings to the ping: 14 of 17 quoted counts are exact and 15 of 15 "gone before" tickets match a real miss. "Gone quiet" tickets lag the data by 2–3 days. But Vesper and Meteor Mite have 0 tickets (Aunt Dot and Kip never file), and overload and unfilled callouts never appear in tickets.
- Quiet 4 vs busiest 5 were identical before 4.2 (3% missed, 23 s to decline). In the first week after release the quiet 4 missed 50% against 15%, likely because they reach the phone in 60–90 s (inferred; accept times aren't in the data). After 3+ days of silence, 73% of pings are missed. Score below ~0.3 plus a stronger neighbour = no recovery (everyone at 0.44 or above recovered). Neighbours absorbed their areas; Southport unfilled went 4% → 22%.
- Scoring: rank = 0.60 proximity + 0.25 acceptance score + 0.15 capability. Score +0.08 taken, −0.12 declined or missed, no decay, so the break-even is taking 60%. The 4.2 reweight actually softened the trap.
- Recommended fix: 90 s window + a miss no longer counts as a "no" + restore the four scores to their 11 Aug level (not 0.5). Optional: score floor about 0.5, home-area priority, extra time after a long silence. Alert rule "pings < half of usual AND 3+ misses in 14 days" had 0 false alarms and caught exactly the four on 19–22 Aug. Biggest data gap: accept times per ping (staff engineer).

### Session notes (Module 4, 15 Oct 2026)

- Code path: availability.py (who's free + travel time) → routing.py (score = 0.60 proximity + 0.25 "says yes" record + 0.15 skills; nobody removed, only ordered) → offer.py (one at a time, 60 s each; a miss is recorded as a decline) → history.py (+0.08 yes, −0.12 no or miss, start 0.5, no decay). All 4.2 routing changes are in config.py: timeout 90→60, proximity 45→60%, record 40→25%.
- Verdict: the 60 s wait plus "miss = no" caused it; the proximity reweight did not (it softened the trap: a score-0 responder now loses to a perfect neighbour up to ~19 min further away, against ~40 min before). Keep the reweight.
- Not distance, not declines: before 4.2 the quiet 4 and the other 12 had the same own-area share (77% vs 79% first in line) and decline rates. In release week the quiet 4 missed 38% of their own-area pings vs 5%. Misses came first; the shortage of pings followed.
- Recovery under current code needs ~6 straight yeses from a score of 0, no declines (even far jobs), at ~1.4 pings/wk, with pings arriving after days of silence (73% missed). Nobody did it. The fix can't rely on responder behaviour: 90 s + miss ≠ no + score restore.
- Published a private artifact, "Dispatch 4.2 Review" (https://claude.ai/artifact/YQwjFmiw4URrFhAr7tqkYL; source `03-rewind/dispatch-4.2-review.html`), for the Director of Product and the engineering manager. Not shared yet; share it from the page's Share menu.
- Pending: wrap-up v2 from a classmate's repo (fnajam325, commit 700ac37) was reviewed but NOT installed. Only wrap-up/SKILL.md differs. Its checkpoint mode says "steps 3 and 5" but the save is step 6; it drops the prompt-exclusion rules; and CLAUDE.md (146 lines) would trigger archiving. The student is choosing A as-is / B with fixes / C skip.
