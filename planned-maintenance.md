---
title: What does a planned maintenance notice mean?
description: "What a planned maintenance notice from PivoCloud means, when it happens, what you may see on your apps and databases, what does not change, and what to do."
last_verified: 2026-11-23
---

## What the notice is

We sometimes move apps and databases to other hardware, for example to replace or service the hardware they run on.
Before we do, we send you a notice by email. It names the apps and databases of yours that will be moved and a
window of dates and times. Each move starts inside that window. A move that is already running when the window ends
finishes just after it.

We send the notice at least 3 days before the window starts, so you have time to read it.

## What you may see

- **A database** is usually unavailable for a few minutes while it moves. Its open connections are closed once. An
  app that reconnects on its own carries on without anyone doing anything.
- **An app** keeps answering while it moves, usually with at most a short interruption.
- **A move can take longer.** If a problem comes up during a move, we step in and finish it ourselves. Until we do,
  the database or app can stay unavailable longer than a few minutes.
- **Some actions are paused during the move.** While an app moves, you cannot deploy, restart or change it. While a
  database moves, its backups, restores, exports and deletion wait. The console says the item is under maintenance;
  try again in a few minutes. See [When we move your app](/apps/manage#when-we-move-your-app) and
  [When we move your database](/databases/connect#when-we-move-your-database).

Your apps and databases are moved one at a time, so you will not see everything stop at once.

## What does not change

- **Addresses.** Your app and database addresses stay the same.
- **Passwords and keys.** Nothing to change or rotate.
- **Your data.** It moves with the database and is not altered.
- **Your bill.** A move is not charged.

## What to do

Nothing. You do not need to stop anything, change a setting or confirm anything. If an action is refused because
the item is under maintenance, try again once the move is over.

If, after the window, your app does not reconnect to its database on its own, restart the app. If it still does not
work, write to support with the name of the app or database, and we will look at it.

## If your database uses an old address

Some databases are still reached through an older address. We cannot move those until you change to the permanent
address. We tell you about that separately, and your database stays where it is until you do. See
[Why did my app or database get a new address?](/new-addresses) for what to change.
