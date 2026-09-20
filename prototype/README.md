# Prototype

Live: **https://chingchung.github.io/income-timer/prototype/**

Widget combinations lab: **https://chingchung.github.io/income-timer/prototype/widgets.html**

Ring + time + money layouts: **https://chingchung.github.io/income-timer/prototype/trio.html**

Weight and tinted rendering: **https://chingchung.github.io/income-timer/prototype/weight.html**

Live Activity: **https://chingchung.github.io/income-timer/prototype/live.html**

`index.html` — a single self-contained file. Open it in any browser, or view
the published version. No build step, no dependencies beyond web fonts.

## What it is for

It exists to answer three questions before any money is spent on a Mac or an
Apple Developer account:

1. **Does the rolling number feel alive at a realistic salary?** Try 15k and
   200k. At 50k/month the cents wheel turns about 7 times a second and the
   dollar wheel every 14 seconds.
2. **Does the instant debit land?** Tap any amount and watch the figure drop,
   then read the cost in minutes of life.
3. **Is the widget still compelling when it only updates once a minute?** This
   is the constraint from [../docs/02-technical-constraints.md](../docs/02-technical-constraints.md),
   and the prototype shows the widget and the in-app view running side by side
   at their real update rates.

## Controls

- **Salary / period / currency**, plus one-tap presets.
- **Work hours**, unpaid-break toggle, net-vs-gross toggle.
- **Time simulation** — scrub any time of day, or run at 1× to 600×, or follow
  the real clock. You do not have to wait until 9am to test.

## Known simplifications

Public holidays are ignored, the simulated week is fixed, expense categories
are inferred from the amount, and the two seeded expenses are labelled as
samples. The rate maths mirrors [../docs/03-calculation-spec.md](../docs/03-calculation-spec.md)
exactly and can be lifted into the Swift shared module.
