# Base plan — RedBus sleeper check, Hyderabad → Chennai, Thu 24 Dec 2026

Status: **draft** (not yet scheduled). Related: `trip-plan-24-28-dec-2026.html` (Plan C, FlixBus 21:15 baseline).

## Goal

Confirm what sleeper inventory actually exists on RedBus for the outbound leg, and shortlist
the buses that leave late enough to be a real alternative to the FlixBus 21:15.

## Search parameters (fixed)

| Field | Value |
|---|---|
| From | Hyderabad |
| To | Chennai |
| Journey date | Thu, 24 Dec 2026 |
| UI departure filter | 18:00 – 24:00 (redBus preset bucket) |
| Report window | 21:30 – 23:45 (filter rows after capture; ignore the rest) |
| Bus type | Sleeper |
| Sort | Departure time, **descending** (latest first) |
| Action | Read-only. No booking, no seat hold, no payment. |

## Output to capture, per bus

- Operator / service name
- Bus type as RedBus labels it (A/C Sleeper, Non A/C Sleeper, Sleeper + Seater, etc.)
- Departure time, arrival time, duration
- Fare (₹) = average of the 10 cheapest available **sleeper** berths in the seat layout
 - Seats left = available **sleeper** berths only (exclude seater berths)

## Target band

Only buses departing **21:30 – 23:45** matter — everything outside this window is dropped.
Compare against the FlixBus 21:15 / ~08:20 arrival / ~₹1,200 / 10 lower berths
baseline in the trip plan.

> Resolved: "9:30 to 11:30" = **21:30–23:30 (9:30–11:30 PM)**, confirmed.
> 25 Nov: window widened to **21:30–23:45** and everything outside it scrapped per request.

## Method (for the scheduled run)

1. Open RedBus Hyderabad → Chennai, set date 24 Dec 2026 if the URL doesn't carry it.
2. Apply: departure 18:00–24:00, bus type Sleeper, sort by departure time descending.
3. Scroll / "load more" until every bus in the window is rendered; read the DOM snapshot,
   not screenshots.
4. Extract the fields above for every bus in the window (fare = avg of 10 cheapest sleeper berths via `/rpw/api/connectingSeatLayout`; seats = sleeper berths only).
5. Post back into **this thread**: the 21:30–23:45 table, latest first, with
   one line of commentary each. No buses outside the window.
6. On captcha / login wall / block / empty result: stop, report exactly what is blocking,
   leave the tab open. No retry loops.
7. Close the tab at the end unless the user is watching.

## Scheduled task

| Field | Value |
|---|---|
| ID | `scheduled-task:d7b614a9-b359-49aa-85f3-dd3f4c9c7f58` |
| Title | RedBus HYD→CHE sleeper check — 24 Dec 2026 |
| Trigger | Daily, 22:00 IST (`fixed_time`) |
| First run | Wed 25 Nov 2026, 22:00 IST |
| Delivery | **This thread** (`bindToCurrentThread`) |
| Context | Continues this session, so later runs can diff against earlier tables |

Prompt is self-contained: route, date, filters, sort order, fields to capture (avg-of-10 sleeper fare, sleeper seats only), the 21:30–23:45
table only, FlixBus baseline, and the captcha/block stop rule. Read-only throughout.

Pause/resume or delete via the task list (Settings → Scheduled tasks) once 24 Dec is booked.

## Run log

- **25 Nov 2026, ~21:52 IST** — blocked, no data. `browse` relay up but extension
  `paired-offline` (Chrome with the browse profile not open / extension off); in-app
  browser also not attached. Nothing scraped. Next attempt 22:00 IST.
- **25 Nov 2026, ~22:00 IST** — success. 41 sleeper buses in 18:00–24:00 (route shows 42
  incl. a 17:00 TGSRTC). 19 in 21:30–23:30, 20 up to 23:45. Table + shortlist saved to
  `redbus-24dec-2026.md`. Baseline discrepancy: FlixBus 21:15 is ₹2,761.90 on redBus,
  not the ₹1,200 in the trip plan.
- **25 Nov 2026, later** — pricing switched to avg of 10 cheapest sleeper berths, seats to
  sleeper berths only (seat-layout API). Window narrowed to 21:30–23:45 only; everything
  outside scrapped from `redbus-24dec-2026.md` and the dashboard already covers just the 20.

