# 04 — Data model & storage

## Storage decision

**Local-only, on-device, no account, no server.** This is a product decision as
much as a technical one: "we never see your money, there is nothing to sign up
for, and we cannot leak what we do not hold" is a strong pitch for a money app,
and it removes the entire backend from v1.

The app and the widget are separate processes, so they need a shared container
scoped to an app group. Both read the same local database; the app writes, the
widget reads and nudges the app to write when its interactive buttons are tapped.

Sync across devices comes later via Apple's own cloud database, which keeps the
"no server of ours" promise intact.

## Entities

### IncomeProfile
The user's pay setup. One active at a time; keep history so past months compute
correctly after a raise.

- id
- salary amount (decimal), period (hourly / daily / monthly / annual), currency
- working weekdays (set)
- start time, end time (wall-clock, not instants)
- unpaid break minutes
- deduct MPF (bool), MPF override amount
- effective from date, effective to date (null = current)
- holiday calendar region

### Expense
- id
- amount (decimal), currency
- category id
- note (optional, short)
- timestamp (instant)
- source: manual / widget quick-add / shortcut / recurring
- deleted flag (soft delete, so stats do not shift under the user)

### Category
Seeded defaults, user-editable. Each has a name, an emoji or symbol, and a
colour. Sensible HK-flavoured seed set: 早餐, 午餐, 晚餐, 咖啡/飲品, 交通,
外賣, 購物, 娛樂, 訂閱, 其他.

### QuickAmount
The presets shown on the widget and the quick-add sheet. Seeded from the user's
own most frequent amounts after a couple of weeks; before that, sensible
defaults ($20 / $40 / $60 / $100).

### DayRecord
A closed day, written once at midnight so history is stable and cheap to query.

- date, gross earned, total spent, net, was working day, leave flag
- rate used that day (so history stays truthful after a raise)

### HolidayCalendar
Static bundled data per region, refreshed with app updates. HK statutory
holidays through at least the next two years must ship in the binary — do not
require a network call to know whether today is a working day.

## What the widget reads

The widget must render with zero network and minimal work. It needs only:

- today's start and end instants, and whether today is a working day
- the per-second rate for the current month
- today's spend total so far
- the quick-amount presets

Write this tiny snapshot to the shared container whenever it changes (profile
edit, expense added, midnight rollover) rather than making the widget query the
full database. The widget then computes its whole timeline from four numbers.

## Rollover

At midnight: close the DayRecord, recompute whether the new day is a working
day, write a fresh snapshot, and refresh the widget timeline. Schedule this as
the natural end of the previous day's timeline so it happens without needing a
background task to fire reliably.

## Privacy posture

- No analytics SDK that carries amounts. Event names only, no values, and make
  analytics opt-in.
- Nothing leaves the device in v1.
- Face ID lock on the app as a setting, off by default.
- When sharing a card, let the user hide the absolute number and share only the
  time-cost version.
