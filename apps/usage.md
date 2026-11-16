---
title: How much memory and processor is my app using?
description: "The Usage tab of your app shows its memory and processor use against its plan for the last hour, 24 hours or 7 days, when it was slowed down or stopped, and what happened to it: restarts, redeploys, crashes, running out of memory, plan changes."
last_verified: 2026-11-16
---

## How much memory and processor is my app using?

Open your app and look at the `Usage` tab. It draws two graphs, memory and
processor, for the last hour, the last 24 hours or the last 7 days. Each graph
shows your app's use against what its plan gives it, so you can see how close
the app is to its limit before it becomes a problem.

## Where to find it

Open your app, then the `Usage` tab, the second tab after `Overview`. The tab has a switch
with three periods: `1 hour`, `24 hours` and `7 days`. `24 hours` is the one it
opens on. When you choose another period, the address of the page keeps it, so a
link to the page opens on the same period.

Reading usage costs nothing and changes nothing about your app.

## Memory

The memory graph is drawn first, because memory is what stops an app. Each point
is the highest memory your app used during a 5 minute stretch. Memory the system
can free by itself, such as file cache, is left out, so the line follows what
your app really holds.

The line is drawn against the memory of your app's plan, in MB or GB. The title
line above the graph says it in one sentence:

```log
Now 310 MB · Peak 380 MB of 512 MB
```

`Peak` is the highest point of the period you chose, and it is read against the
limit your app had at that point. `Now` is the latest point, and it is shown only
when that point is at most 15 minutes old. Otherwise only the peak is shown.

## CPU

The CPU graph is drawn below memory. Each point is the average share of your
plan's processor time that your app used during the same 5 minute stretch.
`100%` is the whole share of your plan, not the whole machine. Hover over the
graph to see the average and the highest value of a point.

```log
Now 12% · Average 18% of plan
```

`Average` is the average of the points that have a figure. A point with no figure
is left out of it, never counted as zero.

## The Plan limit line

Both graphs carry a dashed line labelled `Plan limit`. On the memory graph it is
your plan's memory. On the CPU graph it is the line at 100%, your plan's whole
processor share.

If you change your app's plan, the memory limit line steps up or down at the
moment of the change. Older points stay read against the limit that applied then,
so a peak from before a change is never compared with a limit it was not held to.

## Slowed down

When your app asks for more processor time than its plan's share, it runs slower.
It keeps running and it is not restarted. The CPU graph shows these stretches as
an amber band behind the line, with a key under the graph:

```log
Slowed down at the plan's processor share
```

Hover over a point inside the band and the figures say how much of that stretch
was affected:

```log
Slowed down 12% of this period: the app wanted more than its plan's processor share.
Slowed down less than 1% of this period: the app wanted more than its plan's processor share.
```

If your app is slowed down often, choose a plan with a bigger processor share. See
[What to do when your app stays near its limit](#what-to-do-when-your-app-stays-near-its-limit).

## Gaps

A stretch with no figure is a gap in the line. It is never drawn as zero. A gap
means one of three things: the app was stopped, the app had not started yet, or
the figures did not reach us. When you hover over a gap, the panel says:

```log
No figures recorded at this time.
```

When the app is stopped, suspended or expired, a line under the graphs says since
when:

```log
Stopped since Nov 10, 14:02 CET
```

If no time is known, it says only `Stopped`.

## Marks

Small marks at the top of the graphs show what happened to your app, at the time
it happened. Red marks are crashes and running out of memory. Grey marks are
changes. Hover over a mark to read it. Each sentence ends with the time, in your
own time zone, with its name:

```log
You redeployed the app · Nov 10, 14:02 CET
App redeployed after a push to its repository · Nov 10, 14:02 CET
App redeployed · Nov 10, 14:02 CET
You rolled the app back to an earlier version · Nov 10, 14:02 CET
App rolled back to an earlier version · Nov 10, 14:02 CET
You changed environment variables, app restarted · Nov 10, 14:02 CET
Environment variables changed, app restarted · Nov 10, 14:02 CET
You restarted the app · Nov 10, 14:02 CET
App restarted · Nov 10, 14:02 CET
You changed the app's plan · Nov 10, 14:02 CET
The app's plan was changed · Nov 10, 14:02 CET
App stopped unexpectedly · Nov 10, 14:02 CET
App restarted after a crash · Nov 10, 14:02 CET
App stopped because it ran out of memory (... on its plan) · Nov 10, 14:02 CET
App stopped because it ran out of memory · Nov 10, 14:02 CET
A process inside your app was stopped because the app reached its ... of memory · Nov 10, 14:02 CET
A process inside your app was stopped because the app reached its memory limit · Nov 10, 14:02 CET
```

The sentences that start with `You` are the ones where you did it yourself, from
the console. The others are shown without a name when nobody is named. In the
memory sentences, `...` stands for the memory your app's plan gave it at that time,
which the console shows. The shorter line is shown when that was not recorded.
The process line means your app kept running.

On this tab, a crash and a restart after a crash do not show an exit code. They
read `App stopped unexpectedly` and `App restarted after a crash`. The exit code of
that crash is shown in `Runtime logs`, in the `History` view, on the line at that
time. See [how long are my app's logs kept](/apps/logs).

When two marks are too close to tell apart on the width of your screen, they
merge into one mark that shows how many events it holds. Hover over it to read
them all, oldest first, under a title such as `2 events`.

## Now and refresh

`Now` is the latest 5 minute point, not a live reading. A line under the graphs
says how fresh the figures are:

```log
Updated every 5 minutes · last at 14:05 CET
```

The tab refreshes itself while you keep it open, and when you come back to it
after leaving the page in the background. If a refresh fails, the figures stay
and the line says so:

```log
Could not refresh. Showing figures up to 14:05 CET.
```

## How many points, and how precise

A point is 5 minutes. `1 hour` shows 12 points, `24 hours` shows 288 and `7 days`
shows 2,016. Figures appear about 5 minutes after your app starts.

Figures are rounded to whole MB and whole percent. A memory peak is the highest of
the samples taken during each 5 minute point, so a spike shorter than the
sampling may not show on the graph. A spike that ends the app is different: the
`App stopped because it ran out of memory` mark covers it.

## When there is nothing to show

An app that was never measured, for example a new app that has not run for 5
minutes yet, shows:

```log
No usage recorded yet
Figures appear about 5 minutes after the app starts.
```

An app that has figures, but none in the period you chose, shows
`No figures in this period.` where the title line would be.

If the tab cannot load, it says:

```log
Usage could not be loaded. Try again, and write to us if it keeps failing.
```

and offers a `Try again` button. If the tab says:

```log
Usage is not available for this app.
```

the app does not exist, it is being deleted, or it is not yours.

## How long it is kept and who can see it

The tab shows the last 7 days. We keep the figures a little longer than you can
see, 8 days, and erase the older ones.

Only you can see the usage of your app. Someone else asking for your app's usage
gets the same answer as for an app that does not exist or is being deleted.

## What to do when your app stays near its limit

When the memory line stays close to the `Plan limit` line, or red marks say your
app ran out of memory, choose a plan with more memory. When the amber band shows
often, choose a plan with a bigger processor share. Open your app, choose `More
actions`, then `Change plan`. The app takes the new plan's resources at once, and
a running app restarts briefly to do it. The full steps, and what each plan gives,
are on [what a plan's limits mean](/plans-and-limits).
