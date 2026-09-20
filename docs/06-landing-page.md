# 06 — Landing page

## Why this is Phase 0

It needs no Mac, it can ship this week, it validates the concept with real
traffic, and it collects a waitlist for the app. It is also the best marketing
asset the product will have.

## The hero

The visitor's own number, before they are asked for anything. A salary input,
pre-filled with the Hong Kong median monthly wage, and an odometer that starts
rolling the moment the page loads. No sign-up gate in front of it.

Then the twist below the fold: **the same clock, running for other people.**

## The celebrity ticker

A column of rows, each with a name, a face, and a live-rolling number using the
identical odometer component. The visitor's own row is pinned among them, in
rank order, which is where the joke lands.

The comparison lines are what get shared:

- 「佢用 4 秒賺到你成個月人工」
- 「你由出世到依家賺嘅錢，佢 2 日就賺到」
- A "how long until they earn what you earn in a year" countdown that finishes
  in seconds while you watch.

Include a genuinely useful non-celebrity row: the Hong Kong median monthly wage,
published by the Census and Statistics Department. Most visitors care more about
where they sit against that than against a billionaire, and it makes the page
feel like information rather than only a toy.

## Sourcing rules — important

These are real, identifiable people, and stating their income as fact when it is
an estimate is both wrong and legally careless.

1. **Every figure needs a citable public source**, linked on the page. Executive
   pay from company filings is the strongest category, because it is a published
   legal document. Athlete and entertainer contracts reported by major outlets
   are second best. Net-worth-derived figures are the weakest.
2. **Label the method per row.** "Disclosed 2025 total compensation" is a
   different claim from "estimated from reported net worth change", and the page
   should say which it is on every row.
3. **Never present net worth change as income.** Someone whose shareholding
   rises is not being paid that. If such a row is used for the spectacle, label
   it 「身家變動」 not 「收入」, and explain the difference in one line.
4. **Put a blanket disclaimer in the footer**, and a per-row "source" link.
5. **Do not include private individuals**, and drop any row on request.
6. **Re-verify every figure at build time.** Do not carry numbers over from an
   earlier draft. Add a `lastVerified` date to each row and surface it.

Good row categories, in descending order of defensibility: listed-company CEO
pay from annual filings; Hong Kong Chief Executive and senior official salaries,
which are published; athlete contracts reported by major sports outlets;
entertainer earnings estimated by established business media; billionaire
wealth-change figures, clearly relabelled.

## Method, shown openly

A short "how we calculate" section. For a salaried comparison, divide by working
seconds. For a public figure where the concept of working hours is meaningless,
divide by all seconds in the year, and say so. Mixing the two silently makes the
whole page untrustworthy; showing both makes it interesting.

## Rest of the page

- The instant-debit demo: a "buy a coffee" button that drops the visitor's own
  running number by $40 and shows it climbing back.
- Three screenshots of the app and the widget.
- The time-cost calculator as a standalone toy: enter any amount, get it in
  working time. This is the most shareable single element and is worth its own
  URL for search traffic.
- Email waitlist, one field.
- Footer with the disclaimer, sources, and privacy note.

## Build

Static site, Traditional Chinese first with an English toggle, deployed to any
static host. No backend beyond a hosted form for the waitlist. The odometer
component should be written so it can be lifted into the app's design language
later — same easing, same digit treatment.

Performance matters more than usual: the page has many simultaneous rolling
numbers, and it must not melt a phone. Drive them all from a single animation
loop, and pause rows that are off-screen.
