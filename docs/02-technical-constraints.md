# 02 — Technical reality check

**Read this before anything else. It changes the product design.**

## The headline problem

> An iOS home-screen widget cannot animate a number every second.

This is not a performance issue, it is how WidgetKit works. A widget is not a
running app. It is a set of pre-rendered static snapshots that the system
displays. Your code runs briefly, hands the system a timeline of future views,
and then stops running entirely. Inside a widget there is no run loop, no
`Timer`, no animation driven by your code, and no `TimelineView` refreshing
continuously.

On top of that, the system budgets how often it will wake your widget to ask for
a new timeline — in practice a few dozen times a day for a widget on the home
screen. Burning that budget trying to tick per second would get the widget
throttled and it would go stale.

**Every "money ticking per second on my home screen" concept dies here unless
you plan around it.** Plan around it.

## What actually is possible, and where

Apple states it plainly: "your widget extension is not continually active, even
if the widget is onscreen."

| Surface | Realistic update rate | Notes |
| --- | --- | --- |
| In-app hero screen | 60 fps, true per-second or smoother | The app is running. Full control. This is where the gimmick lives. |
| Home-screen widget, money | Every 5 minutes | Apple's stated floor for timeline entry spacing. See below. |
| Home-screen widget, time elements | Every second | System-rendered date text and timer progress bars, which update without waking your process. |
| Lock Screen / Dynamic Island Live Activity | Money on the same footing; elapsed time every second | Same rendering model as widgets, plus push-update limits. |
| Apple Watch complication | Every ~15 min | Tightest budget of all. Later phase. |

### The five-minute floor

This is the number that shapes the widget, and it is stricter than it first
appears. Apple's guidance on timeline entries is explicit: create entries that
are **at least about 5 minutes apart**, and keep the interval as large as the
content allows.

So the money figure cannot climb every minute. It climbs every five. At a
50,000/month salary that is a jump of about $21 each time. Visible and
satisfying when you happen to be looking, but it is a step, not a roll.

The reload budget itself is separate and more generous than people assume:
roughly 40 to 70 reloads a day for a frequently viewed widget, which is a
reload every 15 to 60 minutes. Crucially, **one reload can return many
entries.** A 9-to-6 day at five-minute spacing is 108 entries, which the system
then steps through on schedule without waking the extension again. The whole
workday therefore costs a single reload. The budget is not the binding
constraint here; the five-minute floor is.

### The two elements that genuinely tick

Some SwiftUI views keep updating while the widget is visible, because the
system re-renders them itself rather than waking your code. There are exactly
two kinds, and both are time-based:

**Self-updating date text.** A text view given a date and a timer, relative or
offset style counts up or down every second, indefinitely, with no timeline
entries at all. It formats time only. There is no way to scale it, multiply it,
or format it as currency.

**A timer-driven progress bar.** A progress view initialised with a date
interval fills continuously and smoothly across that interval, again with no
entries. Given the workday as its interval, it is a bar that visibly creeps all
day.

Put next to the money figure, these carry the liveness: a ticking "worked today
03:42:18", a bar advancing toward knock-off time, and a figure that steps every
five minutes. The eye reads the group as one live meter.

### The one live money number you can have

Because the self-updating text counts real seconds, it cannot show an amount.
But it can show **time until an amount**. A countdown to the next milestone —
"距離今日賺到 $2,000：08:32" — ticks every single second, is genuinely about
money, and costs nothing. It is the only per-second money-related number
available on the home screen, and it is worth building the small widget around
rather than treating as a footnote.

**Do not try to manufacture per-second money digits.** Sub-five-minute entries
work against Apple's stated guidance, will be coalesced or throttled, and leave
the widget stale. The honest design accepts the step and spends its effort on
the elements that really do move.

## Where the real gimmick lives: in-app

The odometer — digits physically rolling, cents blurring, dollars flipping — is
an in-app screen and it should be genuinely beautiful. Full frame-rate SwiftUI.
This is the screenshot that sells the app, the thing shown in the App Store
preview video, and the reason people open it rather than only glancing at it.

The widget's job is to be a persistent, honest reminder that pulls you into the
app. Not to be the show.

## Tap-through interactivity

Widgets on iOS 17 and later can contain real interactive buttons that run a small
piece of code without opening the app. This is how "log a $40 expense" becomes a
single tap from the home screen: the widget shows two or three preset amounts or
a recent-merchants row. That interactivity is a big part of whether expense
logging survives past week one, so it should be in v1, not deferred.

## The other constraint: you are on Windows

WidgetKit is Swift-only and iOS builds require macOS tooling. This machine has
no Swift and no Xcode, and cannot get them. Three honest options:

**Option A — buy or borrow a Mac.** A second-hand Mac mini is the cheapest
serious path. Best development experience by a wide margin, and widget work is
extremely iteration-heavy, so this pays for itself quickly.

**Option B — cloud Mac build, edit on Windows.** Write Swift here, build on a
hosted macOS runner, install on your iPhone over TestFlight. This genuinely
works and costs little. The catch is that your edit-to-see-it-on-screen loop
becomes minutes instead of seconds, and you cannot use the SwiftUI live preview
or the widget simulator at all. For an app whose entire value is animation
polish, that hurts more than usual.

**Option C — cross-platform app shell, native widget extension.** A React Native
or Flutter shell still cannot produce the widget; the widget must be Swift
either way. You end up maintaining two languages and still need macOS to build.
Not recommended — the widget *is* the product, so go fully native.

**Recommendation: build Phase 0 (landing page) now on Windows to validate the
idea and collect a waitlist, and sort out Mac access before starting Phase 1.**

## Accuracy and trust

The number must never be wrong, because the whole premise is that you trust it.
Specific traps:

- **Time zone and daylight saving.** Store instants, compute against the user's
  calendar. Hong Kong has no DST, but travellers do.
- **Clock changes and phone-off periods.** Never accumulate by counting ticks.
  Always derive the figure from the start time, so the answer is correct even if
  the phone was off for six hours.
- **Month boundaries.** The rate is derived per calendar month so that the month
  total lands exactly on the salary. See the [calculation spec](03-calculation-spec.md).
- **Widget and app must agree.** Both read the same shared store and use the same
  calculation code, compiled into a shared module, never duplicated.
