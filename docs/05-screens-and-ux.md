# 05 — Screens & UX

## Onboarding — target 45 seconds, no account

1. **Hook screen.** A demo odometer already ticking with a placeholder salary.
   One line: 「你依家每一秒賺緊幾多？」 Button: 開始.
2. **Salary.** Big number pad. Period selector defaults to 月薪. Currency
   defaults from device region.
3. **Schedule.** Weekday chips (Mon–Fri preselected), start and end time.
   One tap if the default fits, which it will for most users.
4. **Reveal.** The number they will live with: per second, per minute, per hour,
   per day, animated counting up. This is the payoff screen and should be
   over-designed.
5. **Widget prompt.** A short animation showing how to add the widget, because
   most users do not know how. Skippable, and re-findable in settings.

Everything else — MPF, unpaid breaks, holidays, categories — is settings, not
onboarding. Do not ask on day one.

## Home screen (in-app)

Vertical hierarchy:

- **The odometer.** Dominant, centred, rolling digits, roughly half the screen.
  Today's net by default, with a small toggle to view gross.
- **Status line.** 「已經返咗 3 小時 42 分」 with a live-ticking clock.
- **Break-even line**, only when today is negative: 「做到 09:23 回本」.
- **Today's expenses**, a compact list, each row showing the amount and its cost
  in minutes of life.
- **Big add button**, thumb-reachable.

Off-hours the odometer holds yesterday's or today's closing figure and the
status line changes to 「收工」. Non-working days show a different, calmer state
rather than a dead zero — 「今日唔使返工」 and the month-to-date figure instead.

## Quick-add expense — 3 taps maximum

Sheet, opens instantly, number pad focused. Preset amount chips across the top
from the user's own history. Category row below, most-used first. Save.

Taps for the common case: amount chip → category → save. The keyboard path
exists for unusual amounts but should never be the default.

**The confirmation moment matters.** On save, the odometer visibly drops by the
amount, then the time-cost line appears: 「= 9 分 30 秒人生」. That single beat
is the product's emotional core. Do not make it a toast that vanishes.

## Widget layouts

**Small.** Net figure, ticking worked-time line, one dot indicating working or
off. Tap opens app.

**Medium.** Net figure and gross figure, ticking worked-time, today's spend
total, and a row of two or three one-tap quick-add amounts.

**Large.** Adds today's expense list and a week strip showing which days closed
positive.

**Lock Screen.** A single inline figure, plus a circular progress of the workday.

All of them step the money every five minutes and tick the time every second,
as per the [technical reality check](02-technical-constraints.md). The small
widget leads on the countdown to the next milestone, which is the only
money-related figure that moves every second on the home screen.

## Stats

Three tabs: day, week, month.

- Net per day as a bar chart, positive and negative, with a zero line that
  actually means something here.
- Spend by category.
- The headline stat framed in time, not money: 「今個星期你做咗 6 個鐘淨係為咗
  食飯」.
- Streak of days closed positive — light, not gamified into nagging.

## Notifications — restrained

- Workday start: 「開始計數」 with today's rate. Default on.
- Workday end: today's close. Default on.
- Weekly review, Sunday evening. Default on.
- Nothing else. No "you're overspending" guilt pings, ever.

## Share card

Generated image, square and story sizes. Two modes: with the number, or
time-only for the privacy-conscious. Subtle branding and a short URL.

## Accessibility and polish

- The odometer needs a static, readable fallback for reduced-motion users.
- Dynamic type must not break the digits; test at the largest sizes.
- Light mode is the primary design target. Dark mode still has to be designed, not inverted.
- Haptics on expense save, tuned to feel like a small loss rather than a reward.

## Visual direction

Settled from a reference Kenny supplied, and carried in the prototype.

A pale blue-teal ground with white cards, generous corner radii and almost no
shadow. One brand green, used for accent surfaces rather than for the numbers.
A near-black navy for every headline figure and for the active state of any
control. A single coral, reserved strictly for money leaving: expense amounts,
the negative odometer, the break-even warning. Nothing else may use coral, so
that the colour keeps meaning one thing.

Type is one geometric sans for Latin and figures, one Hong Kong sans for
Chinese, and nothing else. Numbers are heavy and tight-tracked; everything
around them is light and quiet. The restraint is the point: the only thing on
screen that should pull the eye is the number.

Light mode is primary. The dark theme is a later, separate design pass.
