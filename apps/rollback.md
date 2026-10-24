---
title: How do I roll my app back to the previous version?
description: "Return your app to the version it ran before the current one from the deployment history, in one click and without a rebuild. What stays the same, what the next push does, when the button is missing, and what it costs."
last_verified: 2026-10-23
---

## Rolling back to the previous version

You pushed, something is wrong, and you want the last good version back now. The
deployment history on your app's `Deployments` tab can do that in one click. Your
app runs the code of the earlier version again within seconds. Nothing is rebuilt,
so a rollback does not wait for a build and cannot fail one.

### How to do it

Open the app, go to the `Deployments` tab, and find the row of the version before
the one that is live. That row carries a button, `Roll back to this`. Press it.
A dialog names the commit your app will run again and repeats what is on this
page. Press `Roll back` to confirm, or `Cancel` to leave everything as it is.

While the rollback runs, the history shows it in progress. When it finishes, the
history has a new row of type `Rollback`, marked `Live`, and the commit on that
row is the version your app runs now.

### What stays the same

A rollback changes the code and nothing else.

- Your environment variables stay as they are now, including any you changed
  after the version you are returning to was built.
- Your attached databases stay attached, with the data they hold now.
- Your domains and your app's address stay as they are.

A rollback does not undo a change to your data. If a bad deploy changed a
database, rolling the code back does not change the database back.

### What happens next

Your next push deploys normally. A rollback does not pause anything and does not
pin your app to the older version. If automatic deploys are on for your branch,
the next push to it builds and replaces the version you rolled back to, exactly as
it would have before.

After a rollback, the row of the newer version you left offers
`Return to this version`. It works the same way and puts that code back, again
without a rebuild.

### Limits

- Only one step is offered: the version before the live one. After a rollback,
  the one step offered is the newer version you left. You cannot go back two
  versions in a row.
- Only versions deployed since this feature arrived can be restored. PivoCloud
  keeps the code of the version before the live one, not all of your versions.
  If that version has been cleared away, the button is not shown until your next
  deploy.
- A rollback is available for an app that is running, or for an app whose last
  deploy failed. It is not available for a stopped app.

### When there is no button

The button is absent in these cases, and each one is normal.

- There is nothing to go back to: the app has had only one deploy, or the earlier
  versions are no longer kept.
- A deploy, a restart or another change is in progress. Wait for it to finish and
  the button returns.
- The app is stopped. Start it first.

### If a rollback fails

Your app keeps running as it did before. A failed rollback does not leave you with
a half-changed app. The console shows a sentence saying why:

- A version that is no longer kept cannot be restored. Pick a version that is
  still listed, or deploy your code again.
- If PivoCloud could not check or read the version right now, try again in a
  minute. If it fails again, contact support with the reference shown.
- If the dialog says the version can no longer be restored, reload the page. The
  history has changed since you opened it and the button now sits on another row.
- If another change is in progress, wait a moment and retry.

The exact sentences, and what to do for each, are on
[why did my deploy fail](/apps/troubleshooting), under the heading about going back
to an earlier version.

### What it costs

A rollback to a running app that has already paid for the current period costs
nothing, the same as a redeploy.

A paid app whose last deploy failed usually keeps the month it already paid, so
rolling it back inside that month costs nothing too.

A rollback is charged like a redeploy only when the app has no paid month: either
none was paid, or the month has ended. The dialog says so before you confirm: it
tells you your wallet is charged, the same as for a new deploy. If your wallet
cannot pay, the rollback is refused with a message asking you to top up, and
your app is left as it was. If a rollback fails, it refunds only a charge it made
itself, and only when nothing of your app is running.

How charges work in general is on
[credit and charges](/billing/credit-and-charges).

### Related pages

- [Reading the deployment history](/apps/deploy): what the columns, `Live` and
  `Latest` mean.
- [What does each button on my app page do](/apps/manage): restart, stop and the
  rest.
