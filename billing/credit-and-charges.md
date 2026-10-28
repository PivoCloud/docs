---
title: What does it cost, and when am I charged?
description: "How PivoCloud credit works in Algerian dinars: adding credit and who approves it, a starting credit claim that is waiting for review, where to find the price of every app plan and database tier, when a charge happens and how long a paid period runs, what the 31 December 2026 promise covers, and what happens if your balance runs out."
last_verified: 2026-10-28
---

## Credit and charges

Everything on PivoCloud is paid for out of one balance, in Algerian dinars. This
page covers what that balance is, how you add to it, where to find what each
thing costs, when the money actually leaves, and what happens if it runs out.

### Your credit, and what it is

One account, one balance. Apps and databases are both paid from it, so there is
nothing else to set up and no card kept on file anywhere.

`Wallet` in the sidebar is where that balance lives. The page header reads
`Wallet` with the balance beside it, and the same page carries the `Top Up`
button and the history of every top-up you have asked for.

A new account starts at zero. When PivoCloud is offering a starting credit, you
claim it once, and [how do I start using PivoCloud](/index) covers claiming it.
The amount is shown on the dashboard and on the `Wallet` page when the offer is
available, so this page does not quote a figure for it.

#### A starting credit claim that is waiting

A claim is reviewed by a person at PivoCloud, so the credit does not appear the
moment you claim it. While it waits, your dashboard shows one line saying that
your starting credit claim is being reviewed and that the credit will be added to
your wallet once it is approved.

There is nothing to press on that line and nothing else to do. The `Free Credits`
card on the `Wallet` page shows the same claim as `Pending Review`. The line
stays until the claim is decided, even if you closed the offer banner earlier.

Once the claim is approved, the credit is on your balance and the line goes
away. If it is not approved, the `Free Credits` card on the `Wallet` page shows
`Claim not approved` with the reason, and the reason is also sent to you by
email. While the offer is available, the same card carries `Claim Again`.

### Adding credit

Credit is added by transfer to our CCP account, by BaridiMob or in person at a
post office, and a person at PivoCloud approves it. No part of that approval is
automatic.

Three of the four steps are yours.

1. Open `Wallet` and press `Top Up`. The dialog is titled `Top Up Wallet`.
   Choose one of the `Quick amounts` or type your own figure into
   `Custom amount (DA)`. The smallest amount you can ask for is 100 DA and the
   largest is 10,000,000 DA.
2. Press `Request Top-Up`. The dialog moves to a second step titled
   `Payment instructions`, which shows you three things: the account
   to pay, the `Amount` to send, and a `Payment Reference` to quote on the
   transfer. Send that exact amount and quote that reference. PivoCloud does not
   publish the account to pay anywhere, on this page or on any other: the one to
   use is the one this step shows you, and it is issued with your request.
3. Your request now sits in the table below, under `Date`, `Amount`, `Status`
   and `Actions`. Press `Upload proof` on its row and attach a screenshot of the
   BaridiMob confirmation, or a photo of your post office receipt.

Then comes the step that is not yours. Someone at PivoCloud reviews the request
by hand, so the credit does not appear the moment you upload the proof.

`Status` is where you watch it. `Pending` means the request is waiting to be
reviewed. `Approved` means the credit is already on your balance. `Rejected`
means it was not accepted, and the reason appears under the row. An email
reaches you when the request is decided, either way, so you do not have to sit
on the page waiting.

If a promotion is running, the approval email and your top-up history show the
extra credit as a separate bonus line. Nothing announces one in advance, so
treat it as a surprise rather than as something to count on.

The amount credited can differ from the amount you asked for. This happens when
the transfer that arrived is not the amount you requested. In that case the
`Amount` in your history shows what was actually credited, and a line under the
row reads, for example, `Requested 2,000 DA, credited 1,900 DA.` followed by a
note from the PivoCloud team explaining why. The approval email names the amount
credited too. Any bonus is computed on the amount credited.

#### Balance adjustments

Sometimes the PivoCloud team changes your balance directly, for example to
refund a charge or to correct a mistake. Every such change appears on the
`Wallet` page, under the top-up history, in a section called
`Balance adjustments`. Each row shows the `Date`, the `Amount` (a plus sign when
credit was added) and a `Note` explaining the reason. The section only appears
once there is at least one adjustment on your account.

### When an action is refused for credit

A deploy, a rollback, a new database or a restore that your wallet cannot cover
is refused. The console shows `Your wallet balance is too low` beside the button
you pressed, with the price of the action, your balance and the amount that is
missing.

Nothing was charged. Your app or database keeps the status it had: an app that
was online stays online with its previous version.

Press `Top up` in the notice. It opens the same top-up dialog as the `Wallet`
page, with the missing amount filled in. When the missing amount is less than
100 DA, the dialog fills in 100 DA, the smallest top-up you can ask for. Once
the top-up is approved and credited, press the button again. Nothing retries on
its own.

### When you are charged

Two rules, and they are not the same rule.

**An app is charged when it first deploys**, not when you create it. Creating an
app takes nothing from your balance. The first deploy opens a paid period and
takes that app's monthly price. Every deploy after that takes nothing at all,
whatever started it: you can redeploy as often as you like inside a period you
have already paid for. An app whose monthly price is zero is never charged.

**A deploy from a push is charged exactly like the deploy button.** If your
wallet does not cover the charge, the push is not deployed, nothing is taken,
and the failed attempt shows in the app's deployment history so you can see it.
Your previous version keeps running.

**A failed deploy is refunded only when it made the charge itself and no earlier
version of your app is still running**, which in practice means a first deploy.
When an earlier version keeps running, you keep the month, because that version
is still being served to you. A failed update never refunds a month you already
paid for and were using. An app that failed after its paid month ended pays a
new month on its next deploy or rollback.

**A database is charged when you create it.** There is no separate deploy step
for a database, so the tier's monthly price leaves your balance at the moment
the database is created.

There is no setup fee and no bandwidth charge.

A paid period is 30 days long in both cases. When it renews, the next period
starts exactly where the last one ended, so there is no gap and no day is paid
for twice. Your app's `Billing` tab shows its `Monthly price` and a
`Next charge` line carrying the amount and the date it falls on.

That tab also carries the `Auto-renewal` switch, and this is the page that
covers it. Turn it off and the app stops renewing: at the end of the period you
have already paid for, it expires instead of opening another one. You can turn
it back on at any time, including on an app that has already expired: switch it
on, make sure the wallet can pay for the plan, then use `Redeploy` to bring the
app back.

Two things this page deliberately does not repeat. What each button on a running
app does to your bill, control by control, is on
[what does each button on my app page do](/apps/manage). What happens to a
charge when a deploy fails, including the part of your history that looks wrong
and is not, is on [why did my deploy fail](/apps/troubleshooting).

### What an app plan or a database tier costs

An app is bought on an app plan, and a database is bought on a database tier.
The price is per app or per database, per month, in dinars. The current price,
memory, processor and included storage of every plan and tier are on
[the pricing page](https://pivocloud.com/pricing), and only there. This page does
not repeat them, so it cannot disagree with the pricing page.

What each limit means, what happens when you reach it, and how to move to a
bigger plan or tier is on [what do my plan's limits mean](/plans-and-limits).

The tiers differ in how far back you can go as well as in how much they hold.
`Starter` brings a database back to one of its backups. `Growth` and `Business`
can bring it back to a moment you choose rather than only to a backup, and how
far back that reaches depends on the tier. What a restore actually produces,
what it costs and how far each tier lets you go is on
[how do I get my data back](/databases/recovery).

### Two names that mean two things

`Starter` is the name of an app plan and, separately, of a database tier: the
same word, two different products, with two different prices. An app plan never
includes a database, and a database tier never includes an app, so what you pay
each month is one line for every thing you created, at the price of the plan or
tier it was created on.

### Your price does not change under you

Apps and databases you buy before 31 December 2026 keep their price and
resources as long as the subscription stays active.

An app or database counts as bought when you chose its plan or tier before that
date and your account had an approved real top-up before it. A top-up that is
still waiting for review at that date does not count. Free credit alone does not
count either. Anything bought after that date has no such promise, and its price
may change later.

When you change an app's plan, the app takes the new plan's resources as they
stand on the day you change it, and its price from your next billing cycle. A
database cannot change tier from the console. Contact support and we move it to
the higher tier. That means a new database on that tier, with a new connection
string and that tier's price.

### If your credit runs out

Nothing is taken away without warning. Four steps, in this order.

1. **A low-balance warning, 5 days ahead.** When a charge is coming that your
   balance cannot cover, an email reaches you 5 days before it is due.
2. **Suspension, when the charge fails.** The app or the database stops serving.
   Nothing is deleted and nothing is lost at this step.
3. **A notice 7 days before deletion, naming the date.** One email per suspended
   app or database, carrying the exact date its data goes.
4. **Permanent deletion, 30 days after suspension.** After that the data is gone
   and cannot be brought back.

Three things worth knowing about that ladder.

**The low-balance warning is one per account, not one per app or database.** If
you are short on two things at once you get one email rather than two, so read
it as a warning about your balance and not as a warning about the one thing it
happens to name.

**The date you are told is the date that is used.** The notice names a day, and
that is the day the deletion runs.

**Deletion can be extended for some accounts. Suspension never is.** Where an
extension applies it delays the deletion only, and when it ends the full wait
starts again from that point, with a fresh notice before anything goes.

The good news is the simplest part of this page. Top up, and your suspended apps
and databases come back on their own, up to what the new balance covers. There
is nothing else to press.
