---
title: Why my backend sleeps and how it wakes
description: "A Pivobase backend sleeps after 15 minutes with no request and wakes by itself on the next one. This page says what keeps a backend awake, what does not run while it sleeps, what Active and Sleeping mean on the backend page, and what to do when you get a 503 with Retry-After."
last_verified: 2026-11-25
---

## What sleeping is

After 15 minutes with no request, your backend sleeps. Its database, its sign-up
and login service and its REST API stop, so a backend that nobody uses takes no
memory and costs nothing to keep.

Your data stays where it is. Nothing is deleted when a backend sleeps, and your
URL and your keys do not change.

## What wakes it

The next request wakes it, with no button to press. Two kinds of request do:

- **A request that carries your anon key or your service key.** This is every
  normal call from `supabase-js`, from your app or from your own server code.
- **The links that finish a sign-in.** When a person is sent back to your
  backend to complete a login, that request wakes it too, even though it carries
  no key.

A request with no valid key, to any other address, gets the same `401` answer as
always and does not wake anything.

The first answer after a sleep takes a few seconds. We measured about 3 to 4
seconds for a backend that was fully asleep. The request is held and then
answered normally, so your app sees a slow first call and not an error. Every
call after that runs at normal speed.

## What keeps it awake

The 15 minutes count from the last activity, so a backend stays awake while
people use it. These keep it awake:

- any request, including the one that woke it
- a request that is still running: a long request is never cut off by sleep
- work we are doing on the backend for you, for example while we regenerate its
  keys

When the last activity is over, the 15 minutes start again.

## What does not run while it sleeps

A sleeping backend runs nothing. Two things you may rely on are affected:

- **Scheduled jobs inside the database**, such as `pg_cron`, do not run while the
  backend sleeps. They run again once something wakes it, and they do not catch
  up on the runs they missed.
- **Anything that waits for a timer inside the backend** does not fire while the
  backend sleeps.

If a task must run at a fixed time whatever happens, trigger it from outside, for
example from your own server with a call that carries your service key. That call
wakes the backend, and the task runs.

## What the backend page shows

The backend page and the list of your backends show one of two words next to the
backend:

- `Active`: the backend is awake and answering.
- `Sleeping (wakes on the next request)`: the backend is asleep. You do not need
  to do anything. Copying your keys and reading your URL work the same while it
  sleeps, and opening the console does not wake it.

The page shows the state at the moment it was loaded. Reload the page to see the
current one.

If the platform has not heard from your backend for a few minutes, the page shows
`Active` rather than an old `Sleeping` state.

While a backend is being created or its keys are being regenerated, the page
shows `Setting up` or `Updating keys` instead, whatever the sleep state.

## If you get a 503 with Retry-After

In rare cases the backend cannot answer right away, and the answer is `503` with a
`Retry-After` header. The body has an error code that says why:

- `backend_waking`: the backend was still starting when the wait ran out. Retry
  once.
- `backend_busy`: the platform is very busy and could not start your backend at
  this moment. Retry after a short wait.
- `backend_unavailable`: the backend failed to start a moment ago, and the
  platform waits before it tries again. Retry after the wait in `Retry-After`.
  The wait starts at 30 seconds and can grow to 5 minutes if the backend keeps
  failing to start. If it does not clear, contact support.

In all three cases, wait for the number of seconds in `Retry-After`, then send the same
request again. Nothing was done by the first attempt, so retrying is safe.

`supabase-js` does not retry by itself. In your own server code and in scheduled
tasks, retry on `503` with the wait the header gives, a few times at most. In a
browser app a single retry after the header's wait is enough for the rare case.
