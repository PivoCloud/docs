---
title: How do I put my own domain on my app?
description: "Add your own domain to a PivoCloud app: the order that works, the CNAME case for a subdomain, the A record (and, for some apps, TXT record) case for the root of your domain, what each status means, and what to do when the check says your DNS is not pointing here yet."
last_verified: 2026-10-13
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

### If your plan does not include custom domains

When your plan does not allow custom domains, the `Domains` tab says they are
available on Starter and higher, or by invitation. An upgrade is one way in. An
invitation is the other, and you can ask for one from the same place.

If the app has no custom domain yet, the tab shows a `Request an invitation`
button. Pressing it sends the request with the app and its plan already
attached. There is nothing else to fill in.

The tab then shows that the request was made, on which day, and that we will
get back to you. One request per account is kept every 7 days: pressing the
button again inside that week does not send a second one.

Nobody is told automatically when an invitation is granted. The `Domains` tab
simply lets you add a domain the next time it loads. An invitation covers every
app on your account, not only the one you asked from.

If an invitation is later withdrawn, the domains you already added keep
working. Only adding a new one is stopped.

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

The value is your app's own address. For an app with an address under
`pivocloud.app` it is that address, for example `shop.pivocloud.app`. For an app
created earlier it is its address under `apps.pivocloud.com`. Either way, copy it
from the `Domains` tab and do not work it out yourself.

For an app with an address under `pivocloud.app`, the check accepts a record only
if it leads to that app's own address, directly or through other `CNAME` records
of yours. A record that points anywhere else, including straight at a PivoCloud
address that is not your app's, is not accepted for that app.

### Case two: the root of your domain, using an A record

This is the case where a name such as `example.com` cannot carry a `CNAME`,
because most DNS providers do not allow one at the root of a domain. For a root
domain the `Domains` tab leads with the A record, not with a `CNAME`, and adds a
line saying that this is a root domain.

The record type is `A`. The value to copy is the one the `Domains` tab shows
next to `A record` for that domain, again with a copy control beside it.

For an app with an address under `pivocloud.app`, the root case needs a second
record, and only appears when it is available for your app. The tab then shows
an `A` record and a `TXT` record. The `TXT` record has the name
`_pivocloud.<your domain>` and, as its value, your app's own address. It is how
the check knows the domain belongs to your app and not to someone else who points
at the same address. Create both records. A `TXT` record you just added can take
up to an hour to be seen.

If the tab shows no `A` record for a root domain, the root case is not
available for your app. The tab then shows the `CNAME` row and a line suggesting
a subdomain such as `www.example.com`. Use a subdomain and, if you want the root
to reach it too, use whatever redirect your DNS provider offers at the root.

A root domain points at the server your app runs on. A subdomain with a `CNAME`
record never needs changing, so `CNAME` is the record we recommend whenever you
have the choice. If you use an `A` record, the value the `Domains` tab shows is
the current one, so copy it from there again rather than trusting a value you
saved earlier.

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

A root domain can show a more specific reason under the domain. Each one names
the record to set, and none of them names a server:

- A domain that points at a PivoCloud address your app does not use. For a root
  domain the message reads:

  ```text
  This domain points at a PivoCloud server this app does not run on. Set its A record to <ip> and try again.
  ```

  and for a subdomain it reads:

  ```text
  This domain points at a PivoCloud server this app does not run on. Set a CNAME record to <your app's address> and try again.
  ```

  Change the record to the value in the message, which is also the value the
  `Domains` tab shows for that domain.

- The A record is right but the `TXT` record is missing or names another app.
  The message reads:

  ```text
  The A record is right. Add the TXT record <name> with the value <value> so we know this domain belongs to this app, then try again.
  ```

  Add that `TXT` record exactly as written. The name and the value are the ones
  shown in the `TXT record` row on the `Domains` tab. It can take up to an hour
  to be seen, so give it time before you change anything else.

- A root domain whose records do not match for any other reason. The message
  reads:

  ```text
  <your domain> is a root domain, so it needs an A record set to <ip>. Most DNS providers do not allow a CNAME record at the root.
  ```

  For an app with an address under `pivocloud.app` it asks for the `A` record
  and the `TXT` record together instead, for example
  `<your domain> is a root domain, so it needs an A record set to <ip>`
  followed by the `TXT` record name and value to set. Set the records the
  message names. If your DNS provider will not let you set an `A` record at the
  root, use a subdomain and the `CNAME` case instead.

Two other messages can appear when you add or verify a domain:

- `That name belongs to PivoCloud. Use a domain you own.` A name inside
  PivoCloud's own domains cannot be used as a custom domain. Use a name you
  own.
- `We could not check this domain right now. Please try again in a moment.` The
  check could not read your app for a moment. Nothing is wrong with your record,
  and the automatic re-check tries again by itself.

### The cap

You can map up to 3 custom domains to one app.

### Taking a domain down

Remove the domain from the `Domains` tab. The mapping goes away and that
hostname stops answering for your app. Your app itself is untouched and keeps
serving on its PivoCloud address and on any other custom domain you have left
in place.

Deleting the DNS record at your provider is worth doing too, so the name stops
pointing somewhere it is no longer served.
