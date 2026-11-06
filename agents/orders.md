---
title: How does my AI agent order a database?
description: "How an AI agent orders a database for you with the Spend level: what an order is, why nothing is charged until you approve it in the console with your password, the 15 minutes and single use, declining, the email, the limit of 3 open orders, free tiers, a short balance, what cutting an agent off does, and what your agent reads back."
last_verified: 2026-11-05
---

{/* launch: remove this notice when database orders for agents is deployed to production */}
Ordering a database from your agent is not open on PivoCloud yet. Your agent can already connect and read.

## What an order is

An order is a question your agent stores for you: "create this database, at this price".
Your agent prepares it, gets a link, and gives you the link. Nothing is charged and no
database exists until you open the link in the PivoCloud console and approve.

An order names four things: the database name, the type (`postgresql`), the region and
the tier. Your agent cannot leave one out. It also sees the price in DA a month and your
wallet balance, so it can tell you what the order will cost before you open the link.

Your agent can prepare an order and check it. That is all. It can never approve one,
decline one, or delete a database. Only you can approve or decline, in the console.

## What your agent needs

The agent needs the `Spend` level. It is separate from `Operate`: `Spend` does not need
`Operate`, and `Operate` does not give `Spend`. It is off until you switch it on, for one
connection at a time. The agent has to ask for `Spend` when it connects, and you turn it on
on the permission page, or later with `Change access` in `Integrations`, under `AI agents`.
See [How do I see, change and cut off my AI agents?](/agents/manage).
An agent that did not ask for `Spend` when it connected cannot be given it afterwards.
It has to connect again.

## What your agent gets back

When your agent prepares an order it reads the order id, the database it asks for, the
price, your balance now and after approval, the approval link, and the time the order
expires. It is told to give you the link, and to check the result later with
`get_database_order`. Preparing the same order twice returns the same order, with the time
that is left, and never a second one.

## Approving in the console

The link opens an order page in the PivoCloud console. It shows the database, what it
costs, your balance now and after, and which connection prepared it. Sign in if you are
asked to.

To approve, type your password on that page. Every order needs its own password, typed
on that order's page. The password field tells your password manager not to fill it. Some
managers ignore that.

When you approve, three things happen together: the database is created, the first month
is charged from your wallet, and the order is closed. If the database cannot be created,
nothing is charged and the order stays open. After the approval, your agent can see the
new database, as it sees the others you shared with it. The database is not connected to
any app.

An approved order is a database charge like any other. See
[What does it cost, and when am I charged?](/billing/credit-and-charges).

### What the password step does not protect against

The password step stops an agent that only has its connection to PivoCloud: that agent has
no password and no way to approve. It does not stop an agent that drives your own browser.
If your agent can operate the browser you are signed in with, on a machine whose password
manager ignores the hint not to fill the field, it could fill the password and submit the
page. It does not help either if you give your password to an agent, or type it into a
browser an agent can read. Only approve an order in a browser you control, after you opened
the link yourself. Never give your password to an agent, and do not give an agent control of
your browser while you are signed in to PivoCloud. A passkey step is planned but not built.

## Declining

You can decline an order on the same page. Declining needs no password and is final: the
order cannot be approved afterwards. Nothing is charged. Your agent sees that you declined,
and is told not to prepare the same order again unless you ask for it. To stop an agent from
preparing orders at all, change its access or cut it off in `Integrations`.

## The 15 minutes and single use

An order lasts 15 minutes from the moment it is prepared. After that it expires and nothing
is charged. The approval link works once: once you approved or declined, or the order
expired, the same link cannot approve anything. If you still want the database, ask your
agent to prepare it again. The page shows the time left, and a message when it runs out.

## The email

PivoCloud sends one email when your agent prepares an order. It says what the database
is, the price in DA (or that it is free), that it is charged monthly, which agent and
connection prepared it, and when the order expires. It links to the order page, where you
approve or decline, and to the page where you cut the agent off. The email approves nothing
by itself. If it does not arrive, the order still exists: check `Integrations`, and decline
it or let it expire. The email is sent on a best effort basis.

## How many orders can wait

A connection can have 3 open orders at once. When it has 3, it cannot prepare another until
one is approved, declined or expires. The agent is told which three are waiting and when the
oldest expires.

## Free tiers

A free tier goes through the same approval. You still type your password on the order page
to approve it. Approving a free tier charges nothing, and the order and the email say that
it is free.

## A short balance

If your wallet holds less than the price, the order page shows how much is missing and lets
you top up the wallet from the order page. Approving stays off until the balance covers the
price. The agent also reads the missing amount when it checks the order.

Only a card payment is credited at once, and it opens the payment page and brings you back.
A transfer is approved by a person at PivoCloud and usually takes longer than the 15
minutes, so the order may have expired, and then your agent has to prepare it again. See
[What does it cost, and when am I charged?](/billing/credit-and-charges).

## A changed price

The price you pay is the tier's price at the moment you approve. If the price changed after
your agent prepared the order, the page says so, with the old and the new price, before you
approve.

## Cutting an agent off

If you cut an agent off by revoking its connection, its open orders are cancelled and
nothing is charged. A cancelled order cannot be approved, and the page says so.

If a connection is frozen, its open orders stay yours to approve or decline in the console.
The frozen agent itself can no longer check them. Lowering `Spend` on a connection does not
cancel its open orders either: you still decide on each one on its page.

## What the order page tells you

The order page says one of these when an order is over:

- `Database created`: the order was approved.
- `Order declined`: you declined it. Nothing was charged.
- `This order expired`: it lasts 15 minutes. Nothing was charged.
- `This order was cancelled`: the agent that prepared it was cut off. Nothing was charged.
- `This order can no longer be approved`: the tier, the region, the name or the type is no longer accepted. Nothing was charged. Ask your agent to prepare a new order.
- `We could not find this order`: it may belong to another PivoCloud account. Check that you are signed in with the account your agent is connected to.

If the page sent your approval but could not get PivoCloud's answer, it says `We could not confirm the approval. Reload this page to see whether the order was approved before you try again.` and keeps both buttons off. It never says nothing was charged then, because the approval may have gone through. If the order expired or was cancelled in the meantime, the page says it could not confirm whether your approval went through: check your wallet history for a charge and your databases list before you ask your agent to prepare the order again.

After several wrong passwords, the page reads `Too many attempts. Try again in [number] seconds.` or `Too many attempts. Try again in [number] minutes.` Approve stays off until you reload the page. Declining still works.

## What your agent reads

The agent reads fixed text. It never contains anything your agent sent, except the
database name, which is only repeated after it passed the name rule. Each text is quoted here
exactly as PivoCloud prints it. Where PivoCloud puts a value, this page writes the kind of
value in square brackets.

### The connection cannot order

The agent did not ask for `Spend` when it connected:

> This connection cannot prepare orders. It did not ask for the Spend level when it connected. Connect again and ask for Spend, then the customer can grant it. Do not retry until then. Tell the customer to remove this connection in PivoCloud under Integrations, add the PivoCloud server again in the agent client, and approve Spend on the consent screen.

The agent asked for `Spend` and you have not switched it on:

> This connection cannot prepare orders. It needs the Spend level. The customer can switch it on for this connection in the PivoCloud console, in Integrations, under AI agents: [console address]/integrations#ai-agents. Do not retry until then.

The connection was frozen, cut off, or had `Spend` switched off while the order was being prepared:

> This connection can no longer prepare orders. It may have been frozen, cut off, or had the Spend level switched off. Nothing was prepared. Do not retry until the customer restores it.

### The call was not accepted

An argument is missing:

> This call does not name: [names]. An order must name the database, its type, its region and its tier. Nothing was prepared. Do not retry without them.

Supabase is asked for:

> Supabase is not offered as a database yet. It is a waiting list the customer joins in the console. Nothing was prepared. Do not retry with this type.

The type is not offered:

> This database type is not offered. The types on offer are: postgresql. Nothing was prepared. Do not retry with the same type.

The name breaks the rule:

> Database names use lowercase letters, digits and hyphens, start and end with a letter or a digit, and are at most 62 characters. The name in this call does not. Nothing was prepared. Do not retry with the same name.

The region is not offered:

> This region is not offered. No PivoCloud machine serves it. Nothing was prepared. Do not retry with the same region.

The tier is not offered, and tiers are on offer:

> This tier is not offered. The tiers on offer are: [tier slug] ([tier name], [price] DA per month). Nothing was prepared. Do not retry with the same tier.

The tier is not offered, and a tier on offer is free (a free tier prints `free` where a priced one prints its price):

> This tier is not offered. The tiers on offer are: [tier slug] ([tier name], free). Nothing was prepared. Do not retry with the same tier.

The tier is not offered, and no tier is on offer:

> This tier is not offered. No tier is on offer right now. Nothing was prepared. Do not retry with the same tier.

The connection already has 3 orders waiting:

> This connection already has 3 orders waiting for the customer: "[name]" ([order id]), "[name]" ([order id]), "[name]" ([order id]). It cannot prepare another until one is approved, declined or expires. The oldest expires at [time]. Do not retry before then.

Any other refusal:

> This order was not accepted. Nothing was prepared. Do not retry with the same arguments.

### The order was prepared

A normal order:

> Order prepared. Nothing is charged and no database exists yet. Database: "[name]", PostgreSQL, tier [tier name] ([tier slug]), region [region]. Price: [price] DA per month, the first month charged at approval. Wallet balance now: [balance] DA, after approval: [balance after] DA. Approval link: [approval link]. It expires at [time], in 15 minutes, and works once. Give this link to the customer: only they can approve it, in the PivoCloud console, with their password. Check the result with get_database_order and order id [order id].

A free tier:

> Order prepared. Nothing is charged and no database exists yet. Database: "[name]", PostgreSQL, tier [tier name] ([tier slug]), region [region]. Price: free. Nothing is charged, now or each month. Wallet balance stays at [balance] DA. Approval link: [approval link]. It expires at [time], in 15 minutes, and works once. Give this link to the customer: only they can approve it, in the PivoCloud console, with their password. Check the result with get_database_order and order id [order id].

A wallet that is short:

> Order prepared. Nothing is charged and no database exists yet. Database: "[name]", PostgreSQL, tier [tier name] ([tier slug]), region [region]. Price: [price] DA per month, the first month charged at approval. Wallet balance now: [balance] DA. [missing] DA missing: the customer must top up before approving. Approval link: [approval link]. It expires at [time], in 15 minutes, and works once. Give this link to the customer: only they can approve it, in the PivoCloud console, with their password. Check the result with get_database_order and order id [order id].

The same order prepared again while it still waits:

> This order was already prepared and is still waiting. Nothing is charged and no database exists yet. Database: "[name]", PostgreSQL, tier [tier name] ([tier slug]), region [region]. Price: [price] DA per month, the first month charged at approval. Wallet balance now: [balance] DA, after approval: [balance after] DA. Approval link: [approval link]. It expires at [time], in [minutes] minutes, and works once. Give this link to the customer: only they can approve it, in the PivoCloud console, with their password. Check the result with get_database_order and order id [order id].

The times are in UTC. When one minute is left, the text says `1 minute`.

### The order was checked

Still waiting for you:

> Order [order id] is waiting for the customer. It expires at [time], in [minutes] minutes. Nothing is charged yet. Check again later; do not prepare it again.

Still waiting, and the wallet is short:

> Order [order id] is waiting for the customer. It expires at [time], in [minutes] minutes. Nothing is charged yet. Check again later; do not prepare it again. Wallet balance now: [balance] DA. [missing] DA missing: the customer must top up before approving.

Approved:

> Order [order id] was approved at [time]. The database "[name]" was created, id [database id], and [price] DA was charged. It is now in this connection's selection, so your database tools can see it. It is not connected to any app.

Approved, on a free tier:

> Order [order id] was approved at [time]. The database "[name]" was created, id [database id], and nothing was charged, it is free. It is now in this connection's selection, so your database tools can see it. It is not connected to any app.

Approved, and the database was deleted afterwards:

> Order [order id] was approved at [time] and [price] DA was charged. The database it created has since been deleted.

Declined:

> Order [order id] was declined by the customer at [time]. Nothing was charged. Do not prepare the same order again unless the customer asks for it.

Expired:

> Order [order id] expired at [time] without approval. Nothing was charged. Prepare a new order only if the customer still wants this database.

Cancelled:

> Order [order id] was cancelled. Nothing was charged. Do not prepare it again unless the customer asks for it.

In a state PivoCloud does not know:

> Order [order id] is in an unknown state. Nothing was charged.
