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
