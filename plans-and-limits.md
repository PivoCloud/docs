---
title: What do my plan's limits mean?
description: "What the memory, processor and included storage of an app plan or database tier mean, what happens when your app or database goes past them, how to move to a bigger plan or tier, and how a change to our plans reaches existing apps and databases."
last_verified: 2026-10-28
---

Every app plan and database tier comes with a set of resources. The current
amounts and prices are on [the pricing page](https://pivocloud.com/pricing), and
only there, so this page never goes out of date when a plan changes. This page
explains what each resource means and what happens when you reach it.

## Memory

**What it means.** Each app has an amount of memory set by its plan. Your app
and everything it starts share that amount.

**When you reach it.** If your app uses more memory than its plan includes, it
may slow down first. Then the system stops one of the processes in your app,
usually the one using the most memory. If it stops your app's main process, your
app restarts by itself. If it stops another process, your app keeps running
without it. When your app restarts like this, the health history in the console
shows `Container exited unexpectedly`, then `Container recovered` once it is
back. PivoCloud does not send you a message before the limit is reached.

**How to move up.** Change the app to a plan with more memory. See
[Moving up](#moving-up).

## Processor

**What it means.** Each app has a share of processor time set by its plan.

**When you reach it.** An app that asks for more processor time than its share
runs slower. It keeps running and it is not restarted.

**How to move up.** Change the app to a plan with a bigger processor share. See
[Moving up](#moving-up).

## Included storage

**What it means.** Each database tier includes an amount of storage. It is an
amount that is included in the price. It is not a limit that blocks you.

**When you reach it.** Nothing happens automatically. PivoCloud measures the
size of each running database. If a database grows past its included storage, we
contact you before anything changes. Your database is not blocked, and you are not
charged anything extra without talking to us first.

**How to move up.** Write to us and we move the database to a higher tier. See
[Moving up](#moving-up).

## Moving up

**An app** changes plan from the console. Open the app, choose `More actions`,
then `Change plan`. The app takes the new plan's resources at once, and a
running app restarts briefly to do it. It takes the new plan's price from your
next billing cycle. The full steps, and what the change does to
your bill, are on
[what does each button on my app page do](/apps/manage). Choosing the plan the
app is already on changes nothing.

**A database** cannot change tier from the console today. Contact support and we
move it to the higher tier. Moving a database to a higher tier means a new
database on that tier, with a new connection string and that tier's price. How
to reach us is on
[how do I get help](/getting-help).

Prices and resources of every plan and tier are on
[the pricing page](https://pivocloud.com/pricing).

## When we change our plans

Apps and databases you buy before 31 December 2026 keep their price and
resources as long as the subscription stays active.

Anything you buy after that date has no such promise, and its price may change
later. When you move an app to another plan, it takes that plan as it is on that
day.
