---
title: What is a Pivobase backend
description: "A Pivobase backend gives your supabase-js or Lovable app a database, sign-up and login, and an API. It is available by invitation today. This page says what works now, what does not work yet, and what to expect from sign-ups and logins."
last_verified: 2026-11-22
---

## What a Pivobase backend is

A Pivobase backend is a ready-made backend for an app that talks to Supabase.
You create one from the console with a single name, and you get back a URL and
two keys. Paste them into your app and it connects, the same way it would
connect to a Supabase project.

A backend gives your app three things:

- a PostgreSQL database that belongs to this backend alone
- sign-up and login for your app's own users
- a REST API over your tables

It works with `supabase-js`, and with apps built in Lovable, which use
`supabase-js` underneath. The page on connecting an app has the exact values for
both.

### By invitation

Pivobase is by invitation today. If your account has been invited, the console
shows a `Backends` item in the `Resources` group. If it does not, Pivobase is not
open to your account yet and there is nothing to enable. We open it to more
accounts step by step, and we say so on the What's new page when that changes.

### Create a backend

1. Open `Backends` and press `Create backend`.
2. Type a name. The name is only for you. It is not part of the URL, and nobody
   else sees it.
3. Press `Create backend` again. The console takes you to the backend's page,
   which says `Setting up your backend` and updates by itself when it is ready.

When it is ready, the page shows your URL, your two keys and a box with the code
to connect your app.

### What works now

- Creating a backend and reading its URL and keys.
- The anon key and the service key, with the service key hidden until you ask
  for it.
- Regenerating both keys when one has leaked.
- Sign-up with an email address and a password, and login with the same.
- The REST API and the database, from `supabase-js` or from plain HTTP.

### What does not work yet

- **Sign-ups are confirmed automatically.** Nobody receives a confirmation
  email. A person who signs up can log in at once, with any email address, so
  your app should not treat the address as proven.
- **Email does not work yet.** A backend sends no email at all. Password reset
  by email does not work, so a user who forgets a password cannot recover the
  account yet. Plan for that in your app.
- **Deleting a backend is not in the console yet.** Contact support if you need
  one removed.

### Login limits

Logins are rate limited, as they are on supabase.co. The limit is counted per
network address, not per user. Two users who sit behind the same address, for
example in one office or one school, share one limit. If a group on one network
all log in at the same moment, some of them may be asked to wait a little.

### What comes next

We are working on more of what a Supabase project offers, one piece at a time.
We do not give dates here. This page lists what works today, and it is updated
when that changes.
