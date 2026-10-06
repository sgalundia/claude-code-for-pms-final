# Console filter persistence tickets: triage

Source: `support_tickets` in rook-database (tickets filed 29 Jun – 7 Sep 2026).
Filter persistence shipped in 4.2 on 12 Aug (CHANGELOG: "Console filter persistence").
Drafted 6 Oct 2026. Nothing has been sent or reassigned yet.

## Classification

16 tickets are about saved filters. 2 came before 4.2 and asked for the feature.
Only 8 of the 14 since 4.2 are cosmetic or harmless. The other 6 describe
handlers working from the wrong responder list without knowing it.

### Cosmetic (fine for tier 4 to file and track)

| Tickets | Issue | Open |
|---|---|---|
| 3061, 3124 | The "filter active" badge is easy to miss | 3124 |
| 3066, 3076 | No way to clear all filters at once | none |
| 3042, 3085 | Filters saved on one computer don't show on another | none |
| 3062, 3100 | Thank-you notes, no issue | 3062 |

### Not cosmetic (keep with the support lead and engineering)

| Tickets | Issue | Why it matters | Open |
|---|---|---|---|
| 3044, 3134 | Saved filters wiped overnight without warning | Handler doesn't know the view changed | both |
| 3064, 3072 | Filters went back to default; "was looking at the wrong list" | Handler works incidents from the wrong list | 3072 |
| 3046, 3115 | Shared computer keeps a colleague's filters | The next shift starts on the wrong list | 3115 |

### Out of scope

- 3005, 3012 were requests for this feature before 4.2 (both closed).
- 3001/3095 (equipment catalog) and 3049/3079 (password email) only mention "filter" or "reset" in passing.

## Draft replies (not sent)

**Badge hard to see (3124, 3061)**
> Thanks for flagging this. You're right that the active-filter badge is easy to miss now that filters stay on between sessions. We've logged it as a console improvement and will let you know when it changes. Until then, a quick check of the filter bar at the start of each shift is the surest way to see what's applied.

**Clear all filters (3066, 3076)**
> Thanks for the suggestion. There isn't a "clear all" option yet, so for now each filter has to be unticked on its own. We've logged it as a console improvement and will update you when it's available.

**Two computers (3042, 3085)**
> Thanks for asking. We're confirming with the team whether saved filters are meant to follow you across computers, and we'll come back to you with a clear answer.

**Thank you (3062, 3100)**
> Thank you, that's great to hear. We've passed it on to the team.

**Filters wiped or reset to default (3044, 3134, 3064, 3072)**
> Thank you for reporting this, and sorry for the trouble. Having your saved filters disappear without warning, and then working from the wrong list, isn't acceptable. We've passed it to the product and engineering team to investigate. Until it's fixed, please check your filters at the start of each shift. We'll update you as soon as we know more.

**Shared computer (3046, 3115)**
> Thanks for reporting this. On a shared computer the console can keep the last person's filters instead of yours. We know that can start a shift on the wrong list, and we've passed it to the product and engineering team. Until then, please check the filters when you take over a shared computer. We'll update you on any change.
