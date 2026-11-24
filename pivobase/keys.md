---
title: When should I regenerate my Pivobase keys
description: "What the anon key and the service key of a Pivobase backend are for, when to regenerate them, and exactly what changes and what breaks when you do. Includes the warning about the service key."
last_verified: 2026-11-22
---

## Your two keys

Every backend has two keys.

- **The anon key** identifies your app to the backend. It is made to be used in
  browser code, because what it can do is limited by row security: a user can
  only reach the rows your policies allow. It is safe in browser code when row
  security is on for your tables.
- **The service key** is the `service_role` key. It ignores row security and can
  read and change all your data.

> **Never put this key in browser code. It bypasses row security and can read and change all your data.**

Keep the service key on your own server, in a secret store or an environment
variable that is not sent to browsers. The console hides it until you press
`Reveal service key`, and it fetches the key only at that moment. Leaving the
page forgets it.

### When to regenerate

Regenerate the keys when one of them may have reached someone who should not have
it. The usual cases:

- A key was committed to a public repository, or pasted in a public place.
- A person who had a key no longer works on your app, for example a former
  collaborator.
- You do not know where a key has been copied, and you want a clean start.

You do not need to do anything else. Regenerating keeps the same backend, the
same URL, the same database and the same users. Only the keys change.

### How to regenerate

Open the backend's page, find the `Regenerate keys` box and press `Regenerate
keys`. A dialog titled `Regenerate both keys?` lists the consequences. Press
`Keep my keys` to stop, or `Regenerate keys` to go ahead. The page then shows
`Updating keys` and updates by itself when the new keys are ready.

### What breaks

Read this before you press the button. It happens at once and it cannot be
undone.

- **Both keys change at the same time.** You cannot keep one and replace the
  other. The old anon key and the old service key stop working immediately.
- **Your app stops working until you update it.** It is still using the old
  keys, so its requests are refused. Copy the new anon key from the console, put
  it in your app, and rebuild if your app reads it at build time. The page on
  connecting an app has the details.
- **Every user signed in to your app is signed out.** They log in again once
  your app has the new key.
- **A few seconds of errors.** The backend restarts to take the new keys, so
  requests can fail for a few seconds while it does.

Anything on your own server that uses the service key needs the new service key
too. Reveal it from the console after the update finishes.

### If the regeneration does not finish

If the page tells you it could not finish regenerating the keys, your old keys
are still the ones in force, and you can try again. If it keeps failing, contact
support.
