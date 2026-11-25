# Scheduled task — DRAFT (not applied yet)

> Edit freely. To put it live: say "apply this draft" and it will replace the prompt
> of `scheduled-task:d7b614a9-b359-49aa-85f3-dd3f4c9c7f58` (title, 22:00 IST slot,
> and thread binding stay unchanged).

| Field | Value |
|---|---|
| Title | RedBus HYD→CHE sleeper check — 24 Dec 2026 |
| Trigger | Daily, 22:00 IST (`fixed_time`) |
| Delivery | This thread (`bindToCurrentThread`) |
| Read-only | Never book, hold seats, or pay |

## Prompt

RedBus sleeper check — Hyderabad → Chennai, Thu 24 Dec 2026. Read-only: never book, hold seats, or pay.

Use the browse browser tools (`tools["browse"].navigate` / snapshot / click / fill / scroll / eval) against the user's real Chrome. Open a new tab and go to https://www.redbus.in/bus-tickets/hyderabad-to-chennai. If the URL does not carry the search, set From = Hyderabad, To = Chennai, journey date = 24 Dec 2026.

Then apply in the RedBus UI:

- Departure time filter: 21:30–23:45 ONLY (everything outside this window is out of scope — do not capture it)
- Bus type: Sleeper (A/C Sleeper and Sleeper+Seater hybrids all count — record the exact label shown)
- Sort: Departure time, DESCENDING (latest departure first)

Scroll and click "load more" / trigger infinite scroll until no further buses appear in the window. Read the rendered DOM via snapshot, not screenshots.

For every bus departing 21:30–23:45 capture: operator/service name, exact bus-type label, departure time, arrival time, duration.

PRICING: do NOT use the listing's "onwards" fare. For each bus, POST to `/rpw/api/connectingSeatLayout` with:

```json
{
  "isCached": false, "bT": 1, "isRedDealApplicable": true,
  "isExactMatch": true, "isReturn": false, "isRO": 0,
  "legs": [{
    "leg": 1, "doj": "2026-12-24", "fromCity": 124, "toCity": 123,
    "opId": "<operatorId>", "routeId": "<routeId>",
    "specialQuota": "NORMAL", "bpId": 0, "dpId": 0,
    "oBoId": 0, "oRouteId": 0, "catCard": false, "isMerge": false,
    "isShortRoute": false, "isSingleLady": false, "isbd": false
  }]
}
```

(`operatorId` / `routeId` come from the searchResults inventory.) From `services[0].seatlist` take seats with `IsAvailable === true` whose `fares.seatType` contains `SLEEPER` (excludes seater berths), sort `fares.amount` ascending: fare = average of the cheapest 10, seats = count of available sleeper berths. Do NOT capture boarding points, ratings, or review counts.

Reply in this thread with:

1. A markdown table of the 21:30–23:45 buses, sorted by departure time descending: Dep, Operator, Type, Arr, Dur, Avg fare ₹ (avg of 10 cheapest sleeper berths), Sleeper seats, plus one sentence of judgement each (arrival time, seat availability).
2. A "Changes since last run" line if earlier numbers exist in this thread; otherwise "first run".

Failure handling: if RedBus shows a captcha, login wall, bot block, or the results list is empty / fails to load, stop at once and report exactly what is on screen with the tab id, leaving the tab open. No retry loops, never attempt a captcha. If the browser extension is not connected, report that rather than retrying.

Finish by closing the tab you opened unless the user seems to be using it. Keep the run under ~10 minutes.

## Open questions

- Should a successful run also refresh `redbus-24dec-2026.html` / the GitHub Pages site, or keep posting tables into this thread only? (Current draft: thread only.)
- Keep the task until 24 Dec is booked, then delete?
