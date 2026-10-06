---
title: How do I see, change and cut off my AI agents?
description: "See every AI agent connected to your PivoCloud account, change what each one can do, rename or revoke a connection, cut every agent off at once, and create a read-only key for a CI job that cannot sign in through the browser."
last_verified: 2026-10-06
---

{/* launch: remove this notice when the agent endpoint is switched on in production */}
Agent connections are not open on PivoCloud yet.

## Where it is

Open `Integrations` in the console and scroll to the section called `AI agents`. It
lists every agent connected to your account and every read-only key you created. Any
change you make here applies on the agent's next call. You do not need to restart your
editor.

To connect an agent in the first place, see [How do I connect my AI agent to PivoCloud?](/agents/connect).

## What each card shows

Connections are grouped by agent. Each card shows the agent's name in quotes and
whether PivoCloud could verify who made it. Under it, each connection shows:

- its name, which you can change
- the levels it holds: `Read`, and `Operate` or `Spend` when you turned them on
- which apps and databases it can reach, or `All apps and databases, including future ones`
- the date it was connected and the time it was last used, or `Never used`

A connection that you revoked, or that ended after 90 days without use, is not shown.

## Change what an agent can do

Press `Change access` on one connection. The panel shows the same choices you saw when
you connected: the levels, and which apps and databases.

- Giving the agent more access asks for your password. Taking access away never does.
- `Read` cannot be turned off. To stop an agent reading, revoke it.
- A level the agent did not ask for when it connected cannot be turned on. The panel
  says so next to that level.
- If you choose `Only these`, tick at least one app or database. To cut the agent off
  completely, revoke it instead.

Press `Save changes`. The agent uses its new access on its next call.

## Rename a connection

Press the pencil next to a connection's name, type a new name of 1 to 60 characters, and
save. The name is only for you. It helps when one agent has several connections, for
example one on each of your computers. Renaming never asks for your password.

## Cut an agent off

There are four ways, from small to large. None of them asks for your password.

- `Revoke` on one connection cuts that connection off.
- `Revoke all {n} connections` on the card of an agent with two or more connections cuts
  every connection of that agent off, where {n} is how many it has.
- `Revoke` on a key cuts that key off at once. A CI job still using it starts to fail.
- `Revoke all` at the top of the section cuts every agent and every key off. It sends
  you an email about it.

After a revoke, the agent's next call is refused. To use the agent again, run its
connect command in your editor and go through the sign-in page once more.

`Revoke all` does not touch the database export tokens in your profile. They keep
working. They are a different credential, described in [How do I call the API with a token?](/api/personal-access-tokens).

## Read-only keys for CI

A key is for a CI job, or another client, that cannot sign in through the browser. If
your agent can sign in, connect it instead. A connection is easier to cut off and
needs no secret copied into a pipeline.

To create one, press `Create read-only key`:

1. Give the key a name, for example the name of the pipeline that will use it.
2. Choose how long it lasts: 30, 90 or 365 days.
3. Choose which apps and databases it can see, or all of them.
4. Press `Create key` and type your password.

You can have up to 10 active keys at the same time.

PivoCloud shows the key once, in a panel that says so. Copy it before you press
`Done, I copied the key`. Afterwards the list shows only `pvck_` and the last four characters, so a
lost key cannot be shown again. A key that expired or was revoked is no longer listed. If you lose it, revoke it and create a new one.

We send you an email when a key is created, and another one week before it expires.

### What a key can do

A key is read only. For the apps and databases you chose, it can:

- see their status, health and deploys
- ask why an app is not working and get PivoCloud's plain answer
- see the names of an app's env vars, never their values

An app can depend on a database. If you chose the app for a key but not its database,
the key learns only that the app has a database it cannot see. It is not told whether
that database runs, and the names the database adds to the app are left out of the
list. Choose the database for the key as well to include them.

A key never reads logs. It never changes, restarts, deletes or orders anything, and it
never spends money. It works only on the PivoCloud MCP endpoint, and it is refused
everywhere else, including the API for database exports.

### Use a key in CI

Store the key as a secret in your CI system, under a name such as `PIVOCLOUD_KEY`. Do
not put it in your repository, in a script, or in a command you type in a terminal.
Your CI job sends it as a bearer token to the PivoCloud MCP endpoint,
`https://api.pivocloud.com/mcp`, in the `Authorization` header of each request:

```http
Authorization: Bearer $PIVOCLOUD_KEY
```

The variable is filled in by your CI system from the secret, so the key itself never
appears in your pipeline file.

Do not use a key to connect an editor or an AI agent that can sign in through the
browser. Use the connect command in [How do I connect my AI agent to PivoCloud?](/agents/connect). That way
there is no key to copy at all.

## If something goes wrong

This page owns the messages of the `AI agents` section of `Integrations` and of the
password panel it opens. Each one is quoted exactly as the console prints it.

**"We could not load your agents and keys. Check your connection, then try again."**
The list could not be read. Press `Try again`. Nothing was changed.

**"We could not save your change. The agent keeps its previous access. Try again."**
A change in `Change access` did not save. Press `Save changes` again.

**"An app or database you chose is no longer available. We removed it from your choice. Check what is left, then save again."**
It was deleted while the panel was open. Check what is left and save again.

**"We could not rename it. Try again."** and **"Use a name of 1 to 60 characters."**
The first means the name did not save. The second means the name is empty after
trimming, or longer than 60 characters.

**"We could not revoke it. It still works. Try again."**
The revoke did not go through, so the agent or key can still be used. Press the revoke
button again.

**"This was already revoked."**
Someone, or another tab, revoked it first. The list is read again and no longer shows it.

**"We could not create the key. Nothing was created. Try again."**
No key exists, so there is nothing to revoke. Press `Create key` again.

**"You already have 10 active keys. Revoke one before creating another."**
Revoke a key you no longer use, then create the new one.

**"Your session has ended. Nothing changed. Sign in again, then try again."**
Your sign-in expired. Sign in again, then repeat the change. The password panel says the
same in its own words: "Your session has ended. Sign in again, then try again."

**"Your browser sent this from an address we do not accept. Nothing changed. Reload the page, then try again."**
The request did not come from the PivoCloud console page. Reload `Integrations` and try
again. The password panel says: "Your browser sent this from an address we do not accept.
Reload the page, then try again."

**"Too many attempts. Wait a minute, then try again."**
The password check allows a few tries and then asks you to wait. When PivoCloud knows how
long, the message names it, for example "Too many attempts. Try again in 45 seconds." or
"Too many attempts. Try again in 2 minutes." Wait, then try again.

**"That password is not correct. Try again."**
Type your password again. It is the password you sign in with.

**"We could not check your password. Try again."**
The password check itself failed, which does not mean the password is wrong. Try again.

**"We could not confirm your password for this change. Nothing changed. Try again."**
The password was accepted but the change still asked for it. Start the change again.

**"Your account cannot confirm a password right now. Contact support."**
Your account is not allowed to confirm a password at this moment.

### A CI job fails after it worked

Open `AI agents` and look at `Read-only keys`. The list shows only keys that still work.
If the key is not there, it expired or was revoked. We email you one week before a key
expires. Create a new key and replace the secret in your CI system.

If the key is there and the job still fails, check that the job sends it as a bearer token
to `https://api.pivocloud.com/mcp`.

A password reset also revokes every read-only key, because a key must not outlive the
sign-in that made it. If you reset your password, create new keys afterwards. AI agents
you connected through the browser are not affected.
