---
title: How do I put my own domain on my app?
description: "Add your own domain to a PivoCloud app: the order that works, the CNAME case for a subdomain, the A record case for the root of your domain, what each status means, and what to do when the check says your DNS is not pointing here yet."
last_verified: 2026-09-10
---

## Your own domain on an app

Every app you deploy already answers on a PivoCloud address. A custom domain
puts your own name in front of it, so visitors reach the same app at a name you
own. Both addresses keep working; the custom one is added, not swapped in.

There are two cases and they need two different DNS records. A subdomain such
as `shop.example.com` uses a `CNAME` record. The root of your domain, such as
`example.com`, cannot carry a `CNAME` at most DNS providers, so it uses an `A`
record instead. Each case is written out below.

### Before you start

Your app has to be deployed and running. A custom domain points visitors at a
live app, so there has to be one for it to point at. If your app has never
deployed, or you stopped it, deploy or start it first and come back.

Your plan has to allow custom domains. They are available on Starter and above.
You do not have to work out whether yours qualifies: the `Domains` tab on your
app tells you. The console shows a decision the server made, so what you see
there is what the server will enforce when you press the button.

### The order, and why it is this way round

Add the domain in PivoCloud first, then create the DNS record. In three steps:

1. Open your app in the console, go to the `Domains` tab, type your domain into
   the field and press `Add domain`.
2. Read the target the tab now shows for that domain. There is a copy control
   beside it.
3. Go to your DNS provider and create the record against that target.

Doing it the other way round is the most common way this goes wrong, and it
fails quietly: a record created before the domain exists in the tab points at a
target you had to guess, and a guessed target is wrong in a way nothing reports
back to you.

### Case one: a subdomain, using CNAME

This is the normal case, for a name such as `shop.example.com`.

The record type is `CNAME`. The value to copy is the one the `Domains` tab
shows next to `CNAME` for that domain, with a copy control beside it. Copy it
from there rather than from anywhere else: it is built from your app's own
identity, so it is different for every app.

At your DNS provider, create a `CNAME` record whose name is the subdomain and
whose value is what you copied.

### Case two: the root of your domain, using an A record

This is the case where a name such as `example.com` cannot carry a `CNAME`,
because most DNS providers do not allow one at the root of a domain.

The record type is `A`. The value to copy is the one the `Domains` tab shows
next to the apex row for that domain, again with a copy control beside it.

That row appears only where PivoCloud has an address to give for the
environment your app runs in. If you do not see it, the root case is not
available to you and the `CNAME` case is the one to use: point a subdomain at
your app and, if you want the root to reach it too, use whatever redirect your
DNS provider offers at the root.

### What happens next, and what you will see

Once the record exists, PivoCloud checks it and moves the domain through four
states. The tab shows the current one as a badge:

- `Pending DNS` means the domain is registered with us and the check has not
  passed yet. This is where every domain starts.
- `Verifying…` means the check passed and the route is being put in place.
- `Active` means the domain is live and serving your app over HTTPS.
- `Error` means a check failed. The reason is shown under the domain.

You do not have to do anything to move it along. A waiting domain is re-checked
by itself every 45 seconds, and once a check passes the route is picked up
within a few seconds. The certificate is obtained automatically and is trusted
by browsers with no step on your side.

There is also a `Verify now` control if you would rather not wait, and it
behaves differently from the automatic re-check in one way worth knowing.
Pressing `Verify now` before your record has spread parks the domain in the
error state with the reason shown. A domain left alone is simply checked again
on the next cycle until it passes. Neither one damages anything, but the manual
one is why a domain sometimes shows an error a minute after you added it.

From the moment a check passes, the domain answers over HTTPS in roughly seven
to ten seconds. That is a range because it is what we measured over several
runs, and one run would not tell you what to expect. Before that comes the part
we cannot time for you: your own DNS change spreading, which is usually the
longer wait of the two.

### If it says the DNS does not point here

The check reports this:

```text
DNS does not point to PivoCloud yet
```

The rest of that message tells you to set the record and try again. Three
things to look at, in this order:

- The record type matches the case you chose. A `CNAME` where the root needs an
  `A` record, or the reverse, fails the check even though the value is right.
- The value matches what the `Domains` tab shows for that domain today. Copy it
  again rather than trusting a value you saved earlier.
- Enough time has passed. A DNS change is not instant: it can take as long as
  the TTL your provider sets on the record, which is often several minutes and
  is sometimes an hour or more.

If all three are right, leave it alone. The automatic re-check keeps running.

### The cap

You can map up to 3 custom domains to one app.

### Taking a domain down

Remove the domain from the `Domains` tab. The mapping goes away and that
hostname stops answering for your app. Your app itself is untouched and keeps
serving on its PivoCloud address and on any other custom domain you have left
in place.

Deleting the DNS record at your provider is worth doing too, so the name stops
pointing somewhere it is no longer served.
