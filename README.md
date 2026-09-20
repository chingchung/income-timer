# Income Timer

**一個讓你看見自己每一秒賺多少錢的 iOS widget。**

上班很悶、度日如年的時候，至少個數字係一直跳緊。

---

## The idea in one paragraph

You tell the app your salary and your work schedule. From the moment your
workday starts, a number on your home screen starts climbing — in real time,
digit by digit, like an odometer. Buy a $40 coffee and the number drops by $40
instantly, then claws its way back. By the end of the day you have a live P&L
of your own life. The rolling number is the hook; the expense tracking is the
reason you keep the app.

## Why it might actually work

Most expense trackers ask you to log spending against an abstract monthly
budget. The feedback is delayed and bloodless. Income Timer makes every expense
hit a number you are emotionally attached to — the number you watched yourself
earn, one second at a time.

The second lever: **every expense is also priced in minutes of your life.**
A $40 coffee is not "$40". It is "9 minutes 30 seconds of sitting at your desk."

## Try it

**https://chingchung.github.io/income-timer/prototype/**

An interactive prototype of the widget and the in-app view, with a time
simulator so the workday can be tested at any hour. No install, no build step.

## Docs

| Doc | What's in it |
| --- | --- |
| [Product brief](docs/01-product-brief.md) | Positioning, target user, core loops, what v1 is and isn't |
| [Technical reality check](docs/02-technical-constraints.md) | **Read this first.** iOS widgets cannot tick every second. What we do instead. |
| [Calculation spec](docs/03-calculation-spec.md) | Exact maths for the rate, daily earnings, net, break-even |
| [Data model](docs/04-data-model.md) | Entities, storage, app-group sharing with the widget |
| [Screens & UX](docs/05-screens-and-ux.md) | Onboarding, home, quick-add, stats, widget layouts |
| [Landing page](docs/06-landing-page.md) | The celebrity-income-per-second gimmick, build plan |
| [Roadmap](docs/07-roadmap.md) | Phases, what ships when, build environment options |
| [Open questions](docs/08-open-questions.md) | Decisions still needed from you |

## Status

Planning. No code yet. Phase 0 (the landing page) is the first thing that can
be built, because it needs no Mac.
