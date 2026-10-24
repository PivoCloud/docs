---
title: What can an agent with Operate do?
description: "What an AI agent with the Operate level can restart, stop, start and change env vars on, what each answer it reads back means, how retries and the limit of 10 changes an hour work, how a frozen connection is lifted, and why an agent cannot redeploy or delete."
last_verified: 2026-10-17
---

{/* launch: remove this notice when restart, stop, start and env var changes for agents is deployed to production */}
Restarting, stopping, starting and changing env vars from your agent is not open on PivoCloud yet. Your agent can already connect and read.

## What Operate allows

An agent connected with the `Operate` level can do these things to an app you shared
with it, and nothing else:

- restart a running app
- stop a running app
- start a stopped app
- set, change or remove its env vars, without ever reading a value

`Operate` is off until you switch it on, for one connection at a time. The agent has to
ask for it when it connects, and you turn it on on the permission page, or later with
`Change access` in `Integrations`, under `AI agents`. See
[How do I see, change and cut off my AI agents?](/agents/manage).
An agent that did not ask for `Operate` when it connected cannot be given it afterwards.
It has to connect again.

The agent can act only on the apps you chose for that connection. An app you did not
choose is answered as `not found`, the same as an app that does not exist.

A restart, a stop and a start answer at once with a job. The agent does not wait for
them to finish. It is told to look at the app again in a few seconds, and it reads
the app's state with its read tools. An env change answers at once too. It comes with a job
only when the app is running. For a stopped app or an app that was never deployed, no job
exists and the answer says so.

## Changing env vars

The `set_env_vars` tool sets, changes or removes the env vars the agent names on one app.
In one call the agent sends the names to set, each with a value, and the names to remove.
Names the agent does not send are never touched.

- **The agent never reads a value back.** The answer lists names, the new version of
  the env vars and when the change applies. It never contains a value, or the size of one.
- **One call is one change.** It counts as one change toward the limit of 10 an hour
  below, however many names it holds, and it shows as one entry in the app's recent
  changes.
- **Setting the same value again counts as a change.** The exception is a repeat, as
  described under Retries. The app's container is switched the safe way, so no answer can
  show whether a stored value was the same.
- **A call can carry about 15 values at the largest size.** One request is limited to
  1 MiB, and one value can be up to 64 KiB. A larger request is turned away before
  anything runs. The agent reads HTTP 413 and the text
  `request body exceeds 1048576 bytes`. Nothing was changed and nothing counted toward
  any limit. The agent should send the change in smaller calls.
- **A stopped app keeps the change for its next start, and an app that was never
  deployed keeps it for its first deploy.** An env change never starts an app.
- **You see each change and you can undo it.** The agent cannot undo anything, and has no
  way to. Only you can, in the console. See
  [How do I set environment variables?](/apps/environment-variables).

## What starting an app resumes

A stopped app is not charged. When an agent starts it, the answer says which billing
resumes: the plan, the price in DA a month, and the new end of the current paid period.
Starting moves the end of the period forward by the time the app spent stopped, the same
as the `Start` button does. An app that has no billing period, or a price of zero, answers
`This app has no charge to resume.`

An agent cannot start an app that has expired. Only you can re-activate it in the
console, because it is a billing step.

## What the agent reads back

The agent reads fixed text. It never contains your app's name, your env vars or your
logs. Each text is quoted here exactly as PivoCloud prints it, and the numbers change
from one answer to the next.

### The action was accepted

A restart answers `Restart accepted. The app is restarting. Check its status with get_app in about 10 seconds.`
A stop answers `Stop accepted. The app is stopping. Check its status with get_app in about 5 seconds.`
A start answers `Start accepted. The app is starting. Check its status with get_app in about 10 seconds.`
followed by the billing line.

A change to env vars on a running app answers
`Change accepted. Set: <names>. Env vars are now at version <n>. The app is switching to the new values. Check its status with get_app in about 10 seconds.`
A part such as `Removed: <names>.` or `Not set, so not removed: <names>.` appears only when the call had such names.

On an app that was never deployed it answers
`Change saved. Set: <names>. Env vars are now at version <n>. This app has not been deployed yet, so it uses them at its first deploy.`

On a stopped app it answers
`Change saved. Set: <names>. Env vars are now at version <n>. This app is stopped, so it uses them when it is started again.`

A call that only asks to remove names that were not set answers
`Nothing changed. Not set, so not removed: <names>. Env vars stay at version <n>.`

### The request was already received

`This request was already received, so it runs once. The app is restarting. Check its status with get_app in about 10 seconds.`
means the same request was sent a second time while the first one still runs. Nothing runs twice.

`This request was already received earlier and has finished, so nothing ran this time. The app is running now. The same request is accepted again after a short wait.`
means the first request is finished and the app is no longer in the state it led to, for
example because you started it again in the console. Nothing ran this time.

A set_env_vars call sent a second time reads
`This request was already received, so it ran once. `
followed by the answer of the first call. If that answer cannot be built again, the agent
reads
`This call looks like one that was just made, but its answer cannot be repeated. Check the app with get_app. Nothing was changed.`

### Another job is running

An action that arrives while a deploy, a restart, a stop, a start or an env change runs for
the same app is refused with a text such as
`A deploy is running for this app (building). Try again in about 15 seconds. You can follow it with get_deployment and id <the deploy's id>.`
The agent may ask again after the wait. Nothing was changed.

### Do not retry

These refusals are permanent for the state the app is in. Each ends with a sentence that
tells the agent not to ask again, because a model that reads only `no` tries again.

- `This app is already running. Do not retry.`
- `This app is already stopped. Do not retry.`
- `This app is stopped, so it cannot be restarted. Use start_app to start it. Do not retry restart_app.`
- `This app has expired. Only the customer can re-activate it, in the PivoCloud console, because it is a billing step. Do not retry.`
- `This app is suspended because the wallet is empty. It starts again by itself when the customer tops up the wallet. Do not retry.`
- `This app is being deleted. Do not retry.`
- `This app has no running version to restart. Ask diagnose_app why. Do not retry.` (the same text says `stop` or `start` for those actions)
- `This app cannot be changed that way in its current state. Do not retry.`

An env var change is refused with the same texts for an app that has expired, is suspended
or is being deleted. It is also refused with one of these, each of which ends by saying
that nothing was changed:

- `<name> is a name PivoCloud reserves, so an agent cannot set or remove it. Nothing was changed. Do not retry with this name.`
- `<name> comes from the database attached to this app, so it cannot be set or removed here. Nothing was changed. Do not retry with this name.`
- `Env var names use capital letters, digits and underscores, start with a letter or an underscore, and are at most 64 characters. At least one name in this call does not. Nothing was changed. Do not retry with the same names.`
- `The value for <name> is larger than 64 KiB, the limit for one value. Nothing was changed. Do not retry with this value.`
- `<name> is named more than once in this call, or is both set and removed. Name each env var once. Nothing was changed. Do not retry with the same names.`
- `This call names no env vars to set or remove. Nothing was changed. Do not retry with the same arguments.`
- `The arguments do not match the input shape of this tool: app is a string, set is a list of objects with a name and a value, both strings, and remove is a list of names. Nothing was changed.`

A name appears in a refusal only when it passed the name check. A refusal never contains a
value.

An app that is being deployed is the one state that is not permanent. The agent reads
`This app is being deployed right now. Check its status with get_app and ask again once it is running.`

### The connection needs Operate

A connection that asked for `Operate` and was not given it reads
`This connection cannot restart, stop or start apps, or change their env vars. It needs the Operate level.`
followed by where you switch it on and `Do not retry until then.`

A connection that never asked for `Operate` when it connected reads
`This connection cannot restart, stop or start apps, or change their env vars. It did not ask for the Operate level when it connected.`
followed by `Connect again and ask for Operate, then the customer can grant it. Do not retry until then.`
and the steps to remove the connection and add PivoCloud again. Switching `Operate` on in the
console cannot help that connection.

## Retries

Agents retry. A connection that drops in the middle of a call sends the same call again.
PivoCloud treats the same action, asked again on the same app by the same connection within
two minutes, as a repeat. It runs once, the agent reads the answer of the first request with
the app's state now, and the repeat does not count toward the limit below.

For a restart, a stop and a start, the rule is per action. It does not look at what happened
in between. If an agent stops an
app, starts it, and then asks to stop it again within two minutes, the second stop is read
as a repeat of the first. Nothing is stopped, and the agent reads that the request was
already received. Wait two minutes, or stop the app in the console.

An env change is a repeat only when it has the same names and the same values, from the
same connection, within two minutes, and no other change has been made to the app's env
vars since. If anything else changed them in between, the call is a new change and it is
written. If an agent sets A, then sets B, then sets A again, the last call is written.

## The brakes

An agent that changes too much is stopped by two brakes.

**The limit of changes.** A connection can make 10 accepted changes in one hour. A restart,
a stop, a start or a call that changes env vars each count as one. A repeat, a refusal and a read do not count. The change
that would be the 11th is not done. The connection is frozen and the agent reads
`This connection reached its limit of 10 changes in one hour, so it is now frozen. This action was not done. The customer has been emailed and can lift the freeze in the PivoCloud console. Do not retry.`

From then on every call of that connection is refused, reads included, with
`This connection is frozen because it made too many changes in one hour. Every call is refused until the customer lifts the freeze in the PivoCloud console, in Integrations, under AI agents. Do not retry.`

PivoCloud sends you one email when a connection freezes. It names the agent and the
connection. Only that connection is frozen. Your other connections, even of the same agent,
keep working. A freeze never ends by itself. You lift it, or you revoke the connection.

**Lifting a freeze.** In `Integrations`, under `AI agents`, the frozen connection shows
`Frozen` and a `Lift freeze` button. Lifting asks for your password. The connection can act
again on its next call, and its count of changes starts again from zero. If you did not
expect the changes, revoke the connection instead. Revoking asks you to confirm, but
never asks for your password. The messages of that screen are on [How do I see, change and cut off my AI agents?](/agents/manage).

If you lift a freeze at the moment a call is being decided, that call can read
`This action was not done, but this connection is not frozen. Try the same call again.`
Nothing was changed and the agent may repeat the call.

**The call budget.** Separate from the limit of changes, a connection can make 120 calls of
any kind in one minute. A connection over that budget is slowed down, never frozen. It reads
`Too many calls from this connection. Wait about 20 seconds, then try again.`
(the number of seconds changes) and the next calls work again once the minute has passed.

## What an agent cannot do

An agent cannot delete an app, a database or a domain, and none of the `Operate` tools does.
Deleting stays in the console, always.

An agent cannot redeploy an app either. A redeploy replaces your running code, so an agent
cannot start one until PivoCloud can swap it safely and roll it back on its own.

## Where the actions show

Each restart, stop and start appears in the app's `Deployment History`, in the `Deployments`
tab, as `Restarted`, `Stopped` or `Started`. An env var change on a running app appears there
as `Env vars updated`. Under that label the row says who asked: `by` and the agent's name in
quotes, then the connection's name in parentheses. If you revoked that connection since, the
line ends with `(revoked)`. An action you took yourself in the console reads `by you`. An
action from a source this version of the console does not know reads `by another source`.

Every env var change, on a running app or not, also appears in `Recent changes`, below the
editor in the `Environment` tab. There the entry says who asked in the same way.
See [How do I manage my app?](/apps/manage).
