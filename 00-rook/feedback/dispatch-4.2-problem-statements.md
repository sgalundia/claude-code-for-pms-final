# Rook Dispatch: problem statements after 4.2

**Sources:** 4 handler interviews (console redesign research, 2–5 Sep 2026) ·
147 support tickets (29 Jun–7 Sep) · 1,696 pings / 1,319 callouts (29 Jun–6 Sep) ·
`dispatch-routing` code. 4.2 shipped 12 Aug.

**Reading the frequency column.** Interviews and tickets come from almost entirely
different people: only Mr. Ambrose appears in both, with a single ticket. Tickets
over-represent handlers who file often and under-represent problems people don't
think are ticket-worthy. Where possible, ping data is the tie-breaker.

---

## Personas

**Handler: "the watcher at the console."** Looks after one or two responders
(e.g. Aunt Dot, Mr. Ambrose, Kip). Works from home, often at night, often doing
something else; glances at the console rather than driving it. Can't accept a
callout, only the responder can, so they care about *seeing* what's happening and
*explaining* it to their responder. Writes in when their responder is hurt, not
when the software is merely awkward.
*Variants:* the multi-responder handler (Kip: two cards side by side, needs to tell
them apart); the shared-desk handler (a colleague covers some nights on the same
login and computer).

**Responder: "the person with the phone."** Gets the ping, has a fixed window to
say yes, is often mid-task (on the stairs, one boot on). Only heard through
handlers. Cares about a fair shot at work and steady volume; reads silence as
"am I still in the system?"

**Rook support and engineering (internal).** Receives the tickets; currently
not replying to callout complaints (45 open, oldest from release day).

---

## Problem statements

Ranked by business impact. **P1–P3 come from 4.2 or were made acute by it.**

### P1: Callouts are withdrawn before responders can answer
Responders need enough time to reach their phone and accept a callout, because
otherwise jobs they're well placed for go to someone else. Today the window is
60 seconds (cut from 90 in 4.2), and neither the responder nor the handler can
see it.

| | |
|---|---|
| **Persona** | Responder (felt), handler (watches it happen, finds out afterwards) |
| **Interviews** | **3 of 4**, all raised it unprompted: "coat on, one boot on — when it went to somebody else" (Ambrose) |
| **Tickets** | **15** from 11 handlers, all since 12 Aug, **all still open**. Two ask outright "Is there a set amount of time before it moves on?" |
| **Data** | Missed pings went from **2% to 18%** (25 of 1,085 → 110 of 611). Missed offers end at exactly 61–63 s, previously 91–93 s. People who say no still answer within 6–40 s |
| **Business impact** | **Callouts never filled doubled: 5.5% → 11.1%** (48 of 879 → 49 of 440). That's an incident with no responder, the core promise of the product. Callouts passed to a second responder: 22% → 33%. Slowest 10% of filled callouts now take 62 s to fill, versus 28 s |

### P2: A missed ping is punished like a refusal, so some responders get starved and others overloaded
Responders need a missed ping, especially one they had no realistic chance to
answer, not to cost them future work. Today the code (`history.py`) penalises a
miss exactly like a "no", and the score never recovers by itself (an open
question in the code since 2019). With misses now 9× more common, responders
are dropping down the list and staying there.

| | |
|---|---|
| **Persona** | Responder (losing work), handler (can't explain it) |
| **Interviews** | **2 of 4**: "one card's dead quiet and the other's on fire" (Kip); Dot's quiet weeks |
| **Tickets** | **30** "gone quiet" tickets from 4 handlers about 4 responders, **all still open**: "starting to wonder if im still even in the system." **0** tickets about overload |
| **Data** | 4 of 16 responders lost more than half their pings (Farlight 12→3/wk, Vesper 14→5, Meteor Mite 11→4, The Undertow 12→4). 5 rose by more than 25% (The Gale 13→19, Captain Vantage 12→16, Stormwrack 13→17, Sgt. Falkirk 10→14, Ironvale 8→11). Two tickets show the spiral: "First one in 9 days and he missed it" |
| **Business impact** | **Responder churn.** Dispatch is the reason responders stay, and starved responders are already asking if they've been dropped. **Burnout** for the overloaded ones. Tickets undercount it: Vesper and Meteor Mite have no tickets at all. Restoring 90 s alone won't fix it, because scores won't recover by themselves |

### P3: Nobody is answering the people reporting P1 and P2
Handlers need a reply when they report that their responder is losing work.
Today all 45 callout-related tickets are open with no visible response, some
for up to four weeks, while handlers follow up again and again.

| | |
|---|---|
| **Persona** | Handler |
| **Interviews** | Echoed: "I'd put that in the report if reports still went anywhere" (Ambrose) |
| **Tickets** | 45 open callout tickets. Ticket volume went from **about 6/week to about 28/week** after 4.2 |
| **Business impact** | Loss of trust on top of the product problem; support load rising every week; handlers are the ones holding responders' morale together |

### P4: Handlers can't see a live callout or why routing did what it did
Handlers need to know when a callout is live for their responder, and why their
responder is busy or quiet, so they can help. Today the console makes the same
sound for every alert, doesn't signal a live callout to the handler, and shows
nothing about how responders are ranked.

| | |
|---|---|
| **Persona** | Handler, especially multi-responder handlers |
| **Interviews** | **3 of 4**: "something that tells me too, not just him" (Dot); "harder to miss, not easier" (Ambrose); one sound per responder (Kip) |
| **Tickets** | **2** (same alert sound for everything). No ticket asks for live-callout visibility |
| **Business impact** | Handlers can't intervene in the 60-second window or answer "why is it quiet?", which is the gap behind P3's ticket volume. Not a 4.2 change, but 4.2 made it matter |

### P5: The console doesn't make it obvious which filter is on
Handlers need to trust that the list they're looking at is the one they meant to
look at. Today saved filters (new in 4.2) can reset silently after updates, are
saved per computer rather than per person, and have an easy-to-miss "active"
badge.

| | |
|---|---|
| **Persona** | Handler (shared-desk and multi-computer variants are hit hardest) |
| **Interviews** | **1 of 4**: "I'd rather it warned me the filter had reset than simply reset it" (Ambrose) |
| **Tickets** | **14** since 4.2 (6 open): 4 silent resets, 4 wrong computer or a colleague's filters, 2 missed badge, 2 "can't clear all", **2 thank-yous** |
| **Business impact** | Low to medium. The feature is valued, but a handler watching the wrong list can miss their responder's activity. Priya called it "noise"; it's more than cosmetic, but not urgent |

### P6: Availability hours use the handler's time zone, not the responder's
Responders working away need their availability set in their own time zone.
Today the console appears to use the handler's.

| | |
|---|---|
| **Persona** | Handler of a travelling responder |
| **Interviews** | 0 of 4 |
| **Tickets** | **2** (Halfmoon, Stormwrack), plus 1 export with wrong times |
| **Business impact** | Small in volume, but it directly affects who gets pinged and when. Halfmoon is one of the "gone quiet" responders, so rule this out before attributing all of P2 to the miss penalty |

### P7: Account and access problems in the console
Handlers need to stay signed in while they're watching, share cover safely, and
keep contact details current. Today they're signed out after about 20 minutes
without a click, password resets take about an hour, cover handlers share one
login, and new phone numbers are rejected as "wrong format".

| | |
|---|---|
| **Persona** | Handler (night-shift and shared-cover variants) |
| **Interviews** | 0 of 4 |
| **Tickets** | **12** (sign-out 2, password 2, second handler access 2, phone number 2, profile photo 2, billing duplicate 2) |
| **Business impact** | Medium. A signed-out handler misses live activity, and a phone number that won't save means pings go to a dead number. Shared logins are also behind part of P5 |

### P8: The console is hard to read and use for some handlers
Handlers glancing at the console from a distance, at night, or with assistive
technology need to be able to read it. Today status badges are small, there's no
dark mode, and several buttons are announced as just "button" by screen readers.

| | |
|---|---|
| **Persona** | Handler (night-shift, older eyes, screen reader user) |
| **Interviews** | **3 of 4**: small text (Dot, Ambrose), dark mode (Kip) |
| **Tickets** | **11** (dark mode 4, badges 2, alerts 2, screen reader 2, tab title 1) |
| **Business impact** | Low to medium, and long-running rather than caused by 4.2. Screen reader support is the exception: one handler is locked out of parts of the console. All of it belongs in the console redesign |

### Out of scope for Dispatch: Rook Supply
Requisition approvals ignore priority, failure reports get no reply, catalog
search is poor, gear is delivered wrong. **1 of 4** interviews (Halloran) and
**26 tickets**. Pass to the Supply PM. Two "maintenance booked on marathon day"
tickets contradict Halloran's "it's gotten smarter".

---

## What this says about the 4.2 story
- **The seasonal dip is real for callout *volume*** (about 140 → about 118 a week) but
  doesn't explain the rise in *missed* pings, which jumped on release day. Neither
  interviews nor tickets mention the season.
- **P1 + P2 are one problem in two parts.** Fixing the timeout without fixing
  how a miss is scored (and resetting scores that were hurt) leaves starved
  responders starved.
- **The change to who gets pinged first is not yet shown to be a problem.** Test it by
  replaying August with misses not penalised. Don't revert it on current evidence.
- **Immediate, cheap action:** a holding reply to the 45 open callout tickets.

## Open questions
1. Does the mobile app show the responder a countdown? (staff engineer)
2. Do travel-time estimates use the same out-of-date map as the incident view? (P6, P7)
3. Has September recovered? This data ends 6 Sep.
4. Which items cut from 4.2 are still Q3 commitments? (Director of Product)
