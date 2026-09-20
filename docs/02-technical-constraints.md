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

| Surface | Realistic update rate | Notes |
| --- | --- | --- |
| In-app hero screen | 60 fps, true per-second or smoother | The app is running. Full control. This is where the gimmick lives. |
| Home-screen widget | Every 60 seconds | Achieved with pre-computed timeline entries, not refreshes. See below. |
| Home-screen widget, seconds element | Every second | Only for *time*-formatted text, via the system's self-updating timer text. |
| Lock Screen / Dynamic Island Live Activity | Money every ~15 min; elapsed time every second | Same constraint as widgets, plus push-update limits. |
| Apple Watch complication | Every ~15 min | Tightest budget of all. Later phase. |

## The technique that makes the widget feel alive

Two elements side by side:

**1. The money number, updated every minute — cheaply.**
When the widget asks for a timeline, we do not return one entry. We return one
entry per minute for the rest of the working day. Because the earnings figure is
a pure function of time (`rate × elapsed seconds`), we can compute all of them up
front with no further code execution. A 9-to-6 day is 540 entries, each tiny.
The system then flips through them on schedule without waking us at all. One
timeline request covers the whole day, so the refresh budget is barely touched.

The number therefore climbs every 60 seconds, all day, reliably. Watching it for
ten seconds you will see it change once. That reads as live.

**2. A seconds-level element that genuinely ticks.**
SwiftUI has date-styled text that the *system* re-renders every second inside a
widget without waking your process. It only formats time, not currency — so we
use it for a ticking "worked today 03:42:18" line directly under the money. The
eye reads the two together as one live meter.

**Do not fake per-second money on the widget.** Showing a number that only
changes each minute while claiming it is per-second is fine; trying to
manufacture per-second digits will either be impossible or get the widget
throttled into staleness.

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
