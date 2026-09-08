---
title: What does each button on my app page do?
description: "Every control on a deployed app in the PivoCloud console, in the order the console shows them, with what each one does and what each one does to your bill before you press it. Plus the two separate places your logs appear."
last_verified: 2026-09-07
---

## Manage your app

Your app's page in the console carries everything you do to an app after it is
created. Seven controls, and two separate places logs appear.

Some of them change what you are charged and some of them do not. Two of the
confirmations tell you so, the rest say nothing about money at all, and you only
read the two after you have already pressed the control. So each control below
gets two answers in the same order: what it does, then what it does to your
bill.

This page covers all seven controls and both log surfaces. Controls appear here
in the order the console renders them: the buttons in the app header first, then
the ones inside the `More actions` menu at the end of that row.

Two buttons in that row do not act on the running app and are not covered here.
`Edit` opens the app's settings. `Top up to resume` appears only when an app has
been suspended for lack of credit, and it takes you to your wallet.

### Deploy and Redeploy

The first button in the header. On an app that has never deployed it reads
`Deploy`. Once a deployment exists it reads `Redeploy`, and while a build is
running it reads `Deploying…` and cannot be pressed.

Pressing it builds your repository again from your deploy branch and replaces
the running container with the result. Your app keeps serving the old container
until the new one is ready.

**What it does to your bill:** nothing, on an app that has already been charged
for the current period. No new charge, no refund, and the paid period runs on
exactly as before. You can redeploy as often as you like.

A first deploy is the exception, and it is the only one. An app with a monthly
price is charged when it first deploys, not when you create it. If your wallet
cannot cover that first charge, the deploy is refused and no money moves.

### Stop

While your app is running, a `Stop` button sits beside `Redeploy` in the same
row. Pressing it opens a short confirmation headed `Stop this app?`, and the
button that carries it out reads `Stop app`.

Your app goes offline. Its address stops answering, its container is stopped,
and nothing about your repository, your settings or your environment variables
changes. It is a pause, not a deletion.

**What it does to your bill:** stopping stops the charge. A stopped app leaves
the billing run entirely, so nothing at all accrues while it is stopped. The
confirmation says the same thing before you press it, and it is right.

### Start

Once your app is stopped, `Start` takes the place of `Stop` in the same row. It
brings the container back on the plan the app is currently on.

**What it does to your bill, and this is the good news the console never tells
you:** starting again moves the **end** of your paid period forward by exactly
the time the app spent stopped. The start of the period does not move. A pause
therefore costs you no paid time at all: whatever you had left when you stopped
is what you have left when you start.

An app that has never been charged has no paid period to move, so starting it
changes nothing.

### Restart

In the `More actions` menu. It bounces your app's container in place: same
image, same build, no rebuild and no clone. Use it when your app is misbehaving
rather than when your code has changed. For new code, use `Redeploy`.

**What it does to your bill:** nothing. A running app's paid period keeps
running straight through a restart.

### Change plan

In the `More actions` menu. It opens a dialog listing the plans you can move to,
with your current one marked, and your wallet balance beside them.

**What it does to your bill:** nothing at the moment you change it. No charge,
no refund, no proration. The new price applies from your next billing cycle, and
the dialog says the same thing where you press the button.

What happens straight away is the resource limits. A running app restarts
briefly to take up the new ones, so expect a few seconds of downtime. A stopped
app records the new plan and takes up its limits the next time you start it.

### Delete

Last in the `More actions` menu, on its own below a divider. It opens a
confirmation naming your app and warning that the action cannot be undone. Your
container, your image, your deployment logs and the app's own record all go.

**What it does to your bill, and nothing in the console states this:** billing
ends immediately, and the paid remainder of the current period is **not**
refunded. Both halves are true at once. You stop being charged from the moment
you delete, and the days you have already paid for are not credited back to your
wallet.

If what you want is to stop paying for an app you may come back to, `Stop` is
the control for that, not `Delete`. A stopped app is not charged, and the paid
time you have left waits for you.

### Where your logs are

There is no `Logs` control. Open the `Deployments` tab and you get two sub-tabs,
`Deployment logs` and `Runtime logs`. They are different places and neither one
contains the other.

- `Deployment logs` is the build. It is everything that happened while
  PivoCloud cloned your repository and built your image, and the panel beneath
  the sub-tab is headed `Deployment Logs`. If a deploy never produced a running
  container, the reason is here.
- `Runtime logs` is your app talking, live, once it is running. It is what your
  process writes while it serves traffic. If your app started and then crashed,
  the reason is here.

Looking for one in the other is the most common way to conclude there is nothing
to see. A crash on startup is in `Runtime logs`, not in the build output. A
build that failed leaves `Runtime logs` empty, because nothing ever ran.

Below both, a `Deployment History` section lists your recent deployments so you
can see which attempt is which.

**What it does to your bill:** nothing. Reading either log costs nothing and
changes nothing about your app.

### If Redeploy seems to do nothing

Pressing `Redeploy` while a deploy is already running produces no feedback at
all. No message, no error, nothing moves on screen. The refusal is real, but the
console does not show it to you, so the natural reaction is to press again.

Open the `Deployments` tab instead and watch the deploy that is already running.
When it finishes, `Redeploy` works normally again.

### If a delete is refused

An app cannot be deleted while a deploy is running. The failure you get back
says only that the delete did not work, with nothing to suggest that waiting
would fix it, and it is the whole of the explanation you are given.

It is a timing refusal and nothing more. Open the `Deployments` tab, wait for
the running deploy to finish, then delete.

`Stop`, `Restart` and `Change plan` are refused the same way while another
change to the app is still in flight, and there the message does tell you to try
again in a moment. Wait a few seconds and press the control again.

### An app you have not deployed yet

A brand new app looks different from the one described above, and none of it is
a fault.

The first button reads `Deploy` rather than `Redeploy`, because there is nothing
to redeploy yet. There are no logs: `Deployment logs` has no build to show and
`Runtime logs` has no container to stream from. `Deployment History` is empty.
`Stop` is absent, because an app that is not running cannot be stopped.

All of that resolves itself the moment your first deploy runs.

### When a message needs explaining

Every failure message PivoCloud can show you, what each one really means, and
what to change, is on [why did my deploy fail](/apps/troubleshooting). Search
that page for the exact sentence you were shown.
