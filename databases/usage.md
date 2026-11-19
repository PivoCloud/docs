---
title: How much memory, processor, storage and connections is my database using?
description: "The Usage tab of your database shows its memory, processor, storage and connections against its tier for the last hour, 24 hours or 7 days, what is left out of each figure, and what it shows while the database is stopped."
last_verified: 2026-11-18
---

## How much is my database using?

Open your database and look at the `Usage` tab. It draws four graphs, `Memory`,
`CPU`, `Storage` and `Connections`, for the last hour, the last 24 hours or the
last 7 days. Each graph shows what your database used against what its tier gives
it, so you can see how close it is to a limit before that becomes a problem.

## Where to find it

Open your database, then the `Usage` tab, the second tab after `Overview`. A
database that is still on the waitlist has no `Usage` tab. The tab
has a switch with three periods: `1 hour`, `24 hours` and `7 days`. `24 hours` is
the one it opens on. When you choose another period, the address of the page keeps
it, so a link to the page opens on the same period.

Reading usage costs nothing and changes nothing about your database.

## Memory

Each point is the highest memory your database used during a short stretch. Memory
the system can free by itself, such as file cache, is left out, so the line
follows what your database really holds. The title line above the graph says it in
one sentence:

```log
Now 215 MB · Peak 230 MB of 512 MB
```

`Peak` is the highest point of the period you chose, read against the limit your
database had at that point. `Now` is the latest point, and it is shown only when
that point is at most 15 minutes old. Otherwise only the peak is shown. The dashed
line labelled `Tier limit` is the memory your tier gives the database.

## CPU

Each point is the average share of processor time your database used during the
same stretch. When your tier has a processor share, `100%` is the whole share of
your tier, not the whole machine, and the line is read against it:

```log
Now 20% · Average 43% of tier
```

When no processor share applies to your database, `100%` is one processor, and the
title line says so:

```log
Now 0% · Average 0% of one processor
```

Hover over the graph to see the average and the highest value of a point. A point
with no figure is left out of the average, never counted as zero.

## Storage

The graph shows how much space your data takes, against the storage included in
your tier:

```log
5.2 GB of 5 GB included
```

When the latest point is not fresh, the line starts with `Peak`:

```log
Peak 5.2 GB of 5 GB included
```

The figure is the same size that the platform uses for its own included-storage
check. Included storage is the amount that is part of your price. It is not a
limit that blocks you: the figure above is past the included amount, and nothing stops. See
[what a plan's limits mean](/plans-and-limits#included-storage). The dashed line
is labelled `Included storage`.

When your database has stayed near a limit for a long time, or has grown past its
included storage, a message above the tabs says so. It is computed from the same
readings these graphs draw. See
[the near-limit message](/plans-and-limits#near-limit-hint). To move to a higher
tier, write to us.

## Connections

The graph shows how many connections your database had open, against the most you
can open at once:

```log
Now 0 · Peak 12 of 97
```

`97` is the real maximum for your database. It is lower than the number the
database server is set to, because a few connections are kept for the platform
itself. Those connections, and the ones the platform opens for its own backups,
archiving and health checks, are left out of the figure, so the number is only
your own application's connections. The dashed line is labelled `Connection limit`.

Connections are not measured for MySQL databases.

## While your database is stopped

A database that is stopped is not measured. A database the console shows as
`Suspended` is stopped in this sense: it is suspended when your wallet balance is
too low to keep it running, and topping up resumes it. See
[how to get your data back](/databases/recovery) for the cause and the way back.
Its graphs keep the figures from before, and the line above each graph says so:

```log
Not measured while stopped · Peak 230 MB of 512 MB
```

A line under the graphs says since when:

```log
Stopped since Nov 10, 14:02 CET
```

If no time is known, it says only `Stopped`. A stretch with no figure is a gap in
the line, never drawn as zero. When you hover over a gap, the panel says:

```log
No figures recorded at this time.
```

## Now and refresh

`Now` is the latest point, not a live reading. A line under the graphs says how
fresh the figures are:

```log
Updated every 5 minutes · last at 14:05 CET
```

The tab refreshes itself while you keep it open. If a refresh fails, the figures
stay and the line says so:

```log
Could not refresh. Showing figures up to 14:05 CET.
```

## When there is nothing to show

A database that was never measured, for example a new database, shows:

```log
No usage recorded yet
Figures appear about 5 minutes after the database starts.
```

A database that was stopped before it was ever measured shows
`Not measured while stopped`, and `Figures appear about 5 minutes after the database is resumed.`
A database that has figures, but none in the period you chose, shows
`No figures in this period.` where the title line would be.

If the tab cannot load, it says so and offers a `Try again` button. The wording of
that message is the same for apps and databases and is given on
[the app usage page](/apps/usage). If the tab says:

```log
Usage is not available for this database.
```

the database does not exist, it is being deleted, or it is not yours.

## What the tab does not show

There is no replica on this tier, so there is nothing to graph for one. Backups are
not drawn here: they are shown on the `Overview` tab of a running PostgreSQL
database, under `Backups`. See [how to get your data back](/databases/recovery).

## Who can see it

Only you can see the usage of your database. Someone else asking for it gets the
same answer as for a database that does not exist.
