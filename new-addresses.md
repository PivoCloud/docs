---
title: Why did my app or database get a new address?
description: "Your app or database now has a second, permanent address. What changed, the date the old address stops, and what to change for a database, an app, and your own domain."
last_verified: 2026-11-12
---

## Your app or database has a new address

We gave your app or your database a second address. The new address keeps
following your app or database wherever we run it, so it will not need to
change again.

**Both addresses work now.** The old one keeps working until a date we give you
(see the next section). If you saved your database certificate to verify it,
read the database section first: that saved copy no longer matches, and your
client refuses to connect until you save the certificate again.

## The date the old address stops

The old address stops answering **14 days after our email to you**. The exact
date is in that email, and on the notice shown on your app or database page in
the console. Everyone gets their own date, counted from their own email.

If you have not found the email, read the notice on the page of the app or the
database. It carries the same date.

## What to change for a database

Use the new address in your connection string, in place of the old one. The
console shows the new connection string on the database page.

- **If PivoCloud linked the database to your app, you change nothing.** We
  switch the app onto the new address for you. If the app was stopped at that
  moment, it uses the new address the next time it starts. If it is running and
  the email asks you to, restart it before the date.
- **If you saved the database certificate to verify it, save it again once.**
  The certificate was issued again so that it carries both addresses, and a
  saved copy of the old one no longer matches. Read it and replace your saved
  file as described in the
  [pin section of the connect page](/databases/connect#pin-your-databases-certificate).
  Until you do, a client that checks the saved certificate refuses to connect.
- **If you did not save the certificate, nothing else changes.** Your client
  keeps working on both addresses until the date.

## What to change for an app

Update the places that use the old address of your app: links, webhooks you
registered with other services, settings in other tools, and the address you
gave to people. Your app answers on both addresses until the date, then only on
the new one.

## If you use your own domain

What you do depends on how your domain reaches your app. The email says which
case is yours.

- **A subdomain that points at the old address by CNAME** (for example
  `app.example.com`). Change the CNAME so that it points at the app's new
  address. The `Domains` tab of the app shows the address to copy.
- **The root of your domain** (for example `example.com`) **pointing at our
  address.** A root domain cannot use a CNAME at most DNS providers, so it moves
  to a different setup:
  1. Point `www.example.com` by CNAME at the app's new address.
  2. At your registrar or DNS provider, set a redirect from the root
     `example.com` to `www.example.com`.
  3. If the email says `www` is missing from your app, add `www.example.com` to
     the app in the console, on the `Domains` tab, before you change the DNS.
- **A domain that already points at the new address.** Nothing to do.

The [custom domains page](/apps/custom-domains) explains each record in detail.

## What happens on the date

The old address stops answering. Anything that still uses it fails to connect or
fails to load. The new address keeps working and is not affected.

If something still uses the old address near the date and you need more time,
write to us before the date through [getting help](/getting-help).
