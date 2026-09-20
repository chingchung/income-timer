# 07 — Roadmap

## Phase 0 — Landing page + waitlist

Buildable on Windows, this week. Static site, the odometer component, the
celebrity ticker, the time-cost calculator, a waitlist field. Ships independent
of everything else and doubles as the marketing site later.

Success signal: people share the time-cost calculator without being asked.

## Phase 1 — The app that justifies the widget

The minimum that is genuinely worth installing.

- Onboarding and income profile
- Shared calculation module, fully unit-tested against the spec
- In-app odometer, at real animation quality
- Expense quick-add and today's list
- Small and medium home-screen widgets, with interactive quick-add
- Local storage in an app group, midnight rollover
- Traditional Chinese and English

Deliberately excluded: stats beyond today, Live Activity, sync, sharing.

## Phase 2 — The reasons to keep it

- Day, week and month stats
- Share cards
- Lock Screen widget and Live Activity during the workday
- Categories, editable presets, recurring expenses
- Shortcuts and Siri: "log forty dollars"
- Notifications

## Phase 3 — Breadth

- Cloud sync across devices, still with no server of ours
- Multiple income sources, hourly and shift work, irregular schedules
- Overtime mode, leave tracking
- Apple Watch complication
- Monetisation

## Monetisation

Free: one income profile, the widget, today's view, seven days of history.
Paid, one-off or small annual: full history and stats, share cards, Live
Activity, multiple profiles, sync, custom themes for the odometer.

A one-off unlock suits this audience better than a subscription, and the app
has almost no running costs to justify a recurring charge. Price it cheaply and
sell volume off the back of the landing page.

## Build environment — decide before Phase 1

Phase 0 needs nothing. Phase 1 cannot start without macOS access in some form.

The realistic options are a second-hand Mac mini, which gives the best iteration
loop by a wide margin and is the recommendation, or a hosted macOS build service
where you edit on Windows and install to your phone through TestFlight. The
second works, but every visual change costs minutes instead of seconds, and this
is an app made almost entirely of visual changes.

Either way you also need an Apple Developer membership to put a widget on a real
device for more than a few days, and a physical iPhone, since widget behaviour
under the system's refresh budget cannot be trusted from the simulator alone.

## Sequencing recommendation

Build Phase 0 now while the idea is hot. Use the waitlist numbers to decide
whether to spend on the Mac and the developer account. If the landing page does
nothing, you have lost a week instead of three months.
