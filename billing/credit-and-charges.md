---
title: What does it cost, and when am I charged?
description: "How PivoCloud credit works in Algerian dinars: adding credit and who approves it, a starting credit claim that is waiting for review, what every app plan and database tier costs, when a charge happens and how long a paid period runs, why your price stays fixed, and what happens if your balance runs out."
last_verified: 2026-09-26
---

## Credit and charges

Everything on PivoCloud is paid for out of one balance, in Algerian dinars. This
page covers what that balance is, how you add to it, what each thing costs, when
the money actually leaves, and what happens if it runs out.

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

Credit is added by BaridiMob transfer, and a person at PivoCloud approves it. No
part of that approval is automatic.

Three of the four steps are yours.

1. Open `Wallet` and press `Top Up`. The dialog is titled `Top Up Wallet`.
   Choose one of the `Quick amounts` or type your own figure into
   `Custom amount (DA)`. The smallest amount you can ask for is 100 DA and the
   largest is 10,000,000 DA.
2. Press `Request Top-Up`. The dialog moves to a second step titled
   `BaridiMob Payment Instructions`, which shows you three things: the account
   to pay, the `Amount` to send, and a `Payment Reference` to quote on the
   transfer. Send that exact amount and quote that reference. PivoCloud does not
   publish the account to pay anywhere, on this page or on any other: the one to
   use is the one this step shows you, and it is issued with your request.
3. Your request now sits in the table below, under `Date`, `Amount`, `Status`
   and `Actions`. Press `Upload proof` on its row and attach a screenshot of the
   transfer.

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

### When you are charged

Two rules, and they are not the same rule.

**An app is charged when it first deploys**, not when you create it. Creating an
app takes nothing from your balance. The first deploy opens a paid period and
takes that app's monthly price. Every deploy after that takes nothing at all:
you can redeploy as often as you like inside a period you have already paid for.
An app whose monthly price is zero is never charged.

**A database is charged when you create it.** There is no separate deploy step
for a database, so the tier's monthly price leaves your balance at the moment
the database is created.

A paid period is 30 days long in both cases. When it renews, the next period
starts exactly where the last one ended, so there is no gap and no day is paid
for twice. Your app's `Billing` tab shows its `Monthly price` and a
`Next charge` line carrying the amount and the date it falls on.

That tab also carries the `Auto-renewal` switch, and this is the page that
covers it. Turn it off and the app stops renewing: at the end of the period you
have already paid for, it expires instead of opening another one. Turning it
back on needs a period that is still running, so an app that has none has the
switch disabled until you deploy it again.

Two things this page deliberately does not repeat. What each button on a running
app does to your bill, control by control, is on
[what does each button on my app page do](/apps/manage). What happens to a
charge when a deploy fails, including the part of your history that looks wrong
and is not, is on [why did my deploy fail](/apps/troubleshooting).

### What an app plan costs

Three plans you can buy today. The price is per app, per month.

| App plan | Price per month | Memory | Processor | Disk | Bandwidth | Apps | Custom domain |
|---|---|---|---|---|---|---|---|
| `Lite` | 1,200 DA | 512 MB | half a core | 5 GB | 10 GB | 1 | no |
| `Starter` | 3,600 DA | 2 GB | 1 core | 10 GB | 50 GB | 3 | yes |
| `Dev` | 7,500 DA | 4 GB | 2 cores | 20 GB | 100 GB | 5 | yes |

The apps column is how many apps that plan lets you run at once. The custom
domain column is whether you may put a name you own in front of an app on that
plan, and [how do I put my own domain on my app](/apps/custom-domains) is the
page for doing it.

### What a database tier costs

Three tiers. The price is per database, per month.

| Database tier | Price per month | Storage | Backups | Backups kept | Service level |
|---|---|---|---|---|---|
| `Starter` | 1,200 DA | 5 GB | daily | 30 days | 99.00% |
| `Growth` | 2,500 DA | 25 GB | daily | 30 days | 99.50% |
| `Business` | 5,000 DA | 50 GB | every 6 hours | 30 days | 99.90% |

The tiers differ in how far back you can go as well as in how much they hold.
`Starter` brings a database back to one of its backups. `Growth` and `Business`
can bring it back to a moment you choose rather than only to a backup, and how
far back that reaches depends on the tier. What a restore actually produces,
what it costs and how far each tier lets you go is on
[how do I get my data back](/databases/recovery).

### Two names that mean two things

Read every price above together with the kind of thing it prices, because two
names collide and so does one price. `Starter` is an app plan at 3,600 DA and,
separately, a database tier at 1,200 DA: the same word, two different products,
two different prices. And 1,200 DA is the price of the `Lite` app plan and also
the price of the `Starter` database tier: the same price, two different
products. An app plan never includes a database, and a database tier never
includes an app, so what you pay each month is one line for every thing you
created, read off whichever of the two tables it belongs to.

### Your price does not change under you

The price of an app or a database is fixed the moment you create it, and it
stays at that figure for as long as that app or that database exists. If our
published prices go up, yours does not.

One thing changes it, and that thing is you. Changing an app's plan takes the
new plan's price as it stands on the day you change it, and that becomes the
app's fixed price from then on. Nothing else moves it. A database has no tier
change at all, so a database keeps the price it was created at for its whole
life.

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
