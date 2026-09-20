# 03 — Calculation spec

Everything the app displays derives from this. Implement it once, in a shared
module used by both the app and the widget.

## Inputs

| Input | Example | Notes |
| --- | --- | --- |
| Salary amount | 50,000 | |
| Salary period | monthly | also: hourly, daily, annual |
| Currency | HKD | multi-currency from day one, it is nearly free |
| Working days | Mon–Fri | per-weekday on/off switches |
| Start time | 09:00 | |
| End time | 18:00 | |
| Unpaid break | 0 or 60 min | default 0, see below |
| Public holidays | HK statutory | on by default for HK users |
| Leave days | user-marked dates | earn nothing, but still paid — see below |
| Deduct MPF | off | HK: 5% of salary, capped at 1,500/month |

## The rate

The rate is computed **per calendar month**, so that a full month of work totals
exactly the stated salary. This matters: a fixed "salary ÷ 22 days" rate drifts,
and months with 23 working days would over- or under-count.

```
paidSecondsPerDay  = (endTime − startTime) − unpaidBreak
workingDays(month) = weekdays matching the schedule
                     − public holidays falling on those weekdays
paidSeconds(month) = workingDays(month) × paidSecondsPerDay
ratePerSecond(month) = monthlySalary ÷ paidSeconds(month)
```

For hourly or daily salary types, the rate is direct and no monthly
normalisation is needed.

### Worked example

Salary 50,000 HKD/month, Mon–Fri, 09:00–18:00, no break deducted, a month with
22 working days.

| Figure | Value |
| --- | --- |
| Paid seconds per day | 32,400 |
| Paid seconds in month | 712,800 |
| Per second | 0.0701 |
| Per minute | 4.21 |
| Per hour | 252.53 |
| Per day | 2,272.73 |

The per-second figure is the one the product is built on. Note that at 0.07/sec
the cents digit changes roughly every 14 seconds, which is *slow* — worth knowing
before designing the odometer. See "display resolution" below.

## Today's earnings

Never accumulate by ticking. Always derive from elapsed time:

```
elapsed(t)  = clamp(t − todayStart, 0, paidSecondsPerDay)
earned(t)   = elapsed(t) × ratePerSecond(currentMonth)
```

If today is not a working day, `earned(t) = 0` unless overtime mode is on.

Before the workday starts, the figure is 0. After it ends, it holds at the full
day's amount until midnight. The held end-of-day figure is important — it is what
the widget shows all evening, and it is the number your evening spending eats
into.

### Unpaid break

Default is **off** — count the full 09:00–18:00. It is simpler, the number is
bigger, and most salaried HK staff do not think of lunch as unpaid. Offer it as a
toggle for correctness-minded users. When on, the earnings figure pauses during
the break window rather than dropping.

## Expenses and net

```
spentToday(t) = Σ expenses with timestamp on today's calendar date
netToday(t)   = earned(t) − spentToday(t)
```

`netToday` is the headline number and **it is allowed to be negative.** The 8am
coffee case the product is built around: at 08:00 earnings are 0, a $40 coffee
puts the day at −40, and at 09:00 the number starts climbing back through zero.

Always keep the gross earnings figure visible as a secondary line, so the user
can separate "how much did I make" from "how am I doing".

## Break-even time

The clock time at which today's earnings cover today's spending so far.

```
breakEven = todayStart + (spentToday ÷ ratePerSecond) seconds
```

Worked: $40 coffee at 0.0701/sec → 570 seconds → you break even at 09:09:30.
With a $60 lunch added, total $100 → 1,427 seconds → 09:23:47.

Show this as a line of copy, not a number in a box: **「你今日要做到 09:23 先回本」**.
When the expense total exceeds a full day's pay, say so plainly rather than
showing a time after the workday ends.

## Price in time

The sharpest feature in the app. Every expense also carries its cost in working
time.

```
secondsOfLife = amount ÷ ratePerSecond
```

| Expense | At 0.0701/sec |
| --- | --- |
| $40 coffee | 9 min 30 s |
| $60 lunch | 14 min 16 s |
| $1,200 dinner | 4 h 45 min |
| $8,000 phone | 3.2 working days |

Round sensibly: under an hour show minutes and seconds, under a day show hours
and minutes, above that show working days. Never show "0.0004 working years".

## Display resolution

At a normal salary the cents digit moves every ~14 seconds, which will feel
static if the odometer only shows two decimals. Options, in order of preference:

1. **Show more decimals on the in-app odometer** — four decimal places on a
   de-emphasised trailing pair. The tail blurs constantly, the dollars climb
   slowly. This is the visually correct answer and costs nothing.
2. Show a secondary per-second rate readout that is visibly alive.
3. Let high earners look fast and normal earners look slow, and accept it.

On the widget, show two decimals only, since it updates per minute anyway.

## Rounding and money type

Use a decimal type, never a binary floating-point type, for stored amounts and
totals. Compute the rate and elapsed-time product in floating point for display
only, and round to the currency's minor unit at the moment of display. Stored
expenses are exact decimals. Monthly totals must reconcile to the cent.

## Edge cases to handle explicitly

- Overnight shifts, where end time is before start time.
- Changing salary mid-month — rate change applies from that date forward; do not
  retroactively rewrite earlier days.
- Leaving a job / unemployment gap — pause mode, earns nothing, expenses still log.
- Paid leave. A leave day is paid but not worked. Simplest honest treatment: the
  day earns nothing live, and the amount is absorbed into the other working days
  of that month. Mark leave days so the monthly reconciliation stays exact.
- Public holidays falling on a weekend.
- The user starting the app mid-day — they should see today's accrued amount
  immediately, not start from zero.
