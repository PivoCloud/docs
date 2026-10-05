---
title: Why did my deploy fail?
description: "The messages PivoCloud shows when a deploy does not work, what each one really means, and what to change. Search this page for the exact sentence you were shown and it will take you to that failure."
last_verified: 2026-10-05
---

## Find the sentence you were shown, then read what it really means

A failed deploy puts two different sentences in front of you about the same
failure, and they are not the same words. A red panel at the top of the app page
carries a short explanation written for a human. The build log underneath it
carries the sentence PivoCloud wrote at the moment the deploy stopped, which is
longer and names your own paths.

Both are quoted here for every failure. Whichever one you copied, searching this
page for it takes you to the right entry.

**If the red panel and the log disagree, believe the log.** The explanation in
the red panel is worked out from the text of the log, and it can land on the
wrong one. The log is a record of what happened. The panel is an interpretation
of it.

## What is on this page, and what is not

This page is the index of the messages that **stop a deploy**: the ones that
leave your app undeployed and put a red panel on the app page, plus the ones
that refuse a deploy before it starts. It is an index of those. It is not an
index of everything PivoCloud can tell you.

The page also explains the answers your AI agent can give when you ask it why an
app is not working. Those are in the section
`What your AI agent can tell you about an app`, further down. Each answer there
has its own heading, because your agent sends you a link straight to it.

Two families of message are deliberately not here.

**The build agent's own rejections of a repository URL.** PivoCloud checks your
repository URL when you save the app and again when you press `Redeploy`, so a
URL it will not accept is refused at that moment and never reaches a build. What
you read is the wording of that check, and that wording is quoted on this page.
There is no second set of sentences from the build to look for.

**Failures that leave your app running but not reachable at its public
address.** These do not fail the deploy at all. Your app is up; only its address
is not configured yet. They appear in the app's public URL panel on the
`Overview` tab, not in the deployment logs, and this page does not cover them.

## When PivoCloud cannot tell you why

The red panel reads:

```text
Deployment failed. Please check the logs below for details.
```

This is what you get when PivoCloud did not recognise the failure from the log
text. It has nothing specific to say, so it says nothing specific.

**The sentence that does say something is hidden for this one.** Under the red
panel there is a `Technical details` toggle. Click it to open it, and inside is
the sentence PivoCloud wrote when the deploy stopped. That is the one to read,
and the one to paste into a search or into a chat with your assistant.

**And one case where there is nothing to read at all.** If the failed deploy
recorded no message, no red panel appears. The app shows as failed with no
explanation beside it. Open the `Deployments` tab, pick the attempt that failed,
and read its build log directly.

## Before the build starts: Redeploy refused because of the repository

When you press `Redeploy`, PivoCloud checks your repository before it builds
anything. If that check fails, nothing is built and nothing is charged. The
answer carries `Repository validation failed` and one of the eight sentences
below, and the sentence is the part worth reading.

```text
The repository URL format is invalid. Please use a valid GitHub HTTPS URL (e.g., https://github.com/owner/repo).
```

The URL is not in a shape PivoCloud can use. Copy it out of your browser's
address bar on the repository page rather than typing it.

```text
SSH-style Git URLs are not supported. Please use an HTTPS URL (e.g., https://github.com/owner/repo).
```

You gave the `git@github.com:owner/repo.git` form. Use the `https://` form of
the same repository.

```text
Only GitHub repositories are supported. Please provide a GitHub HTTPS URL.
```

The URL points somewhere that is not GitHub. GitHub is the only host PivoCloud
builds from today.

```text
This repository is private or does not exist. If it's private, connect the PivoCloud GitHub App or add a Fine-Grained PAT with Contents: read-only access. Otherwise, verify the URL is correct.
```

PivoCloud asked GitHub for the repository and GitHub did not show it. From the
outside, a private repository and a repository that was never there look the
same, which is why one sentence covers both.

```text
This repository is marked private but has no credentials attached. Connect the PivoCloud GitHub App or add a Fine-Grained PAT with Contents: read-only access.
```

You told PivoCloud the repository is private and then gave it no way to read it.
This one is exact: attach credentials.

```text
The repository could not be found. Please verify the URL and ensure the repository exists.
```

The owner or the repository name does not resolve. Check the spelling of both,
and check the repository has not been renamed or deleted.

Two more sentences arrive in the same place. They are quoted here only up to the
point where they change subject, because the platform writes them with a
punctuation mark this documentation does not use. The rest of each one is given
below in plain words.

```text
Token rejected
```

The token was read and GitHub refused it. Check it has not expired and that it
grants `Contents: read-only` on **this** repository, not on a different one.

```text
Could not reach GitHub
```

PivoCloud could not reach GitHub at all. Nothing is wrong with your repository
or your token. Wait a minute and press `Redeploy` again.

## The clone

### PivoCloud could not clone your repository {#clone_failed}

The red panel reads:

```text
Failed to clone the repository. Please verify the URL is correct and the repository is public.
```

**Read the second half of that sentence loosely.** Your repository does not have
to be public. PivoCloud deploys private repositories, and
`The repository is private, or PivoCloud cannot read it` says how.

Three different build-log sentences produce this same red panel:

```text
git clone failed: The repository could not be found. Please verify the URL and ensure the repository exists.
```

```text
git clone failed: Network error during clone. Please check your connection and try again.
```

```text
git clone failed: Git clone failed: exit status 128
```

The first means GitHub did not show the repository: check the owner and the
repository name, and check whether it is private. The third is the catch-all,
carrying the raw exit code, and its cause is in the lines above it in the build
log.

**What the agent's answer says.** All three give the same answer,
`clone_failed`, and it lists which one it was. It tells you to check the
repository URL and name, and if they are right, to wait a minute and press
`Redeploy`, because a clone can also fail on a temporary network problem.

**A badly formed URL lands in the second one.** If your repository URL is not
merely wrong but malformed, the clone fails in a way that is reported as a
network problem. So if you read the network sentence and your connection is
fine, read the repository URL character by character before you look at anything
else.

### The repository is private, or PivoCloud cannot read it {#repo_access_lost}

The red panel reads:

```text
This repository appears to be private or inaccessible. Provide a Fine-Grained Personal Access Token (Contents: read-only) to deploy private repositories.
```

The build log for the same failure reads:

```text
git clone failed: Authentication failed. Check that the repository token is valid and has Contents: read-only access.
```

**What it really means:** PivoCloud reached GitHub, offered whatever credential
it has for this app, and GitHub said no. Either there is no credential, or the
one attached does not open this repository.

**What to do, and what not to do.** Do not make your repository public.
PivoCloud deploys private repositories and there are two supported ways to let
it read yours.

- **Connect the PivoCloud GitHub App** and pick the repository from the list.
  This is the shorter path and there is nothing to renew.
- **Or attach a fine-grained personal access token.** On the app form, open the
  `Advanced` section, turn on `Private repository`, and paste the token into the
  `Fine-Grained Personal Access Token` field. The token needs exactly one
  permission on exactly one repository: `Contents: read-only`. Give it nothing
  else.

A token is a password. Paste it into that field and nowhere else, and never
commit one to your repository.

### The repository URL does not look valid {#invalid_repo_url}

The red panel reads:

```text
The repository URL does not appear to be a valid public GitHub HTTPS URL.
```

The word "public" in that sentence is not a requirement. What PivoCloud could
not do is read the URL as a GitHub HTTPS address.

The build log sentence that pairs with this panel is:

```text
git clone failed: The repository URL format is invalid. Please use a valid GitHub HTTPS URL.
```

Without the clone prefix, the same sentence reads:

```text
The repository URL format is invalid. Please use a valid GitHub HTTPS URL.
```

**In practice you are unlikely to be shown either of them for a malformed URL.**
A URL that is badly formed rather than merely wrong is reported as a network
problem instead, so the sentence you actually meet is the network one, under
`PivoCloud could not clone your repository`. Both are quoted here so that a
paste of either lands on this entry.

The sentences shown when the same URL is refused before a deploy starts are more
specific, and they are the ones to act on. They are quoted under
`Before the build starts: Redeploy refused because of the repository`.

## The build

### PivoCloud could not find a Dockerfile to build {#dockerfile_missing}

The red panel reads:

```text
We couldn't find a Dockerfile in the repository root. Please ensure your repo contains a Dockerfile.
```

The build log says the same thing with your own paths in it, and this is the
version worth reading. It begins with `No Dockerfile at ` followed by the file
it looked for, then ` (build context: ` followed by the directory it looked
inside, so a whole instance reads like
*"No Dockerfile at `<path>` (build context: `<dir>`)."*

There is a second form of this failure. If the path you gave points at something
that is not a file, a directory of that name for example, the log says
*"Dockerfile path `<path>` is not a regular file (build context: `<dir>`)."*
instead. Same cause, different mistake: the first means nothing is there, the
second means something is there and it cannot be built.

Read the red panel's wording loosely. It says "repository root", but PivoCloud
does not require a Dockerfile at your repository root at all. What failed is the
path it actually resolved, which is the one printed in the log.

**Two settings decide that path, and one of them is wrong.** `Root directory` is
the directory the build can see, and it defaults to your repository root.
`Dockerfile path` is the file to build, relative to `Root directory`, and it
defaults to `Dockerfile`. Both live on the app page. Compare the path printed in
the log against what you set. A Dockerfile at `api/Dockerfile` with `Root directory`
left blank needs `Dockerfile path` set to `api/Dockerfile`. The same file with
`Root directory` set to `api` needs `Dockerfile path` set to `Dockerfile`.
How the two combine, with a worked example, is on
[what your repository needs](/apps/deployment-contract).

**The log then hands you the answer.** After that sentence it appends
`Dockerfiles found in this repository:` and lists the ones it did find, each in
quotes. Compare that list against the path it says it was looking for and the
mistake is usually obvious.

That list is taken from your **whole repository**, not only from inside the
`Root directory` you chose. That is deliberate, and it is the single most useful
thing in the message: the commonest cause of this failure is a Dockerfile that
exists and simply sits outside the directory you pointed the build at. If your
Dockerfile is in that list but the build still could not find it, the two
settings are the thing to change, not your repository.

The list stops at ten entries and does not tell you that it stopped. Add up what
the message does show you: the paths it printed, plus the number in its own
`and N more` note if it carries one. If that total reaches ten, read the list as
a sample and search your repository for the rest.

That `and N more` note counts only the entries the message dropped to stay
inside its own size limit. It never counts the ones the search stopped looking
for at ten.

A list below ten did not hit that limit. It is still not a promise that your
repository holds nothing else. The search behind it is bounded. It looks only a
few directory levels down from the repository root, it never descends into the
directories a build generates or vendors into, and on a very large repository it
stops early. `node_modules` and `dist` are two examples of the directories it
skips rather than the whole set, and the set is a platform detail that can
change.

If the Dockerfile you expected is not in the list, check whether it sits deeper
than that or inside one of those generated directories. Then set
`Dockerfile path` to it directly instead of waiting for the list to name it.

One more place this sentence turns up. The failure recorded against the
deployment itself is the same text with `docker build failed: ` in front of it,
so if you copied it from there rather than from the log, everything above still
applies.

**What to do:** fix whichever of the two settings is wrong, then redeploy. The
button on the app page reads `Redeploy`. Nothing else needs changing and you do
not need to push a commit for the new settings to take effect.

### The build itself failed {#build_failed}

The red panel reads:

```text
The Docker build failed. Please check the logs below for details.
```

**This panel is deliberately empty of detail.** The build ran and something
inside your own Dockerfile failed: a package that would not install, a compile
error, a command that returned non-zero. PivoCloud has no opinion about what
your build does, so it has nothing to add. Read the build log from the bottom
upwards. The last command that ran before the failure is the one to look at.

One case reaches this panel that is not an error in your Dockerfile at all. If
`Dockerfile path` points at something that exists but is not a file, the log
carries the `is not a regular file` wording rather than a build error. That is a
settings mistake, and `PivoCloud could not find a Dockerfile to build` covers it.

### The build ran out of time {#build_timeout}

The red panel reads:

```text
Your build ran out of time and was stopped. Redeploy to continue: the build resumes from the layers already cached, so each attempt gets further.
```

The build log carries the same fact with the limit in it:

```text
Build timed out after 30m0s, the current build limit on this platform. Redeploy to continue: the build restarts from the layers already cached, so it gets further each time.
```

**Press `Redeploy`, and keep pressing it until the build completes.** This is
not a retry in the hopeful sense. The layers your build already finished are
kept, so the next attempt does not repeat them: it starts where the previous one
stopped and reaches a later stage. Repeat and it eventually gets all the way
through.

Note what this does **not** promise. A build that runs out of time runs for the
whole limit every time, because the limit is what ends it, so two failed
attempts take the same wall-clock time even though the second did more work.
What changes between attempts is how far the build gets, not how long you wait.
The attempt that finally completes is the short one.

**You do not have to hit the limit to find out what it is.** The first lines of
every build log carry a line beginning `Build limit for this deployment: `,
followed by the limit for that deployment and the time it would be cancelled. So
you can read your budget before you start waiting for it.

If your build keeps running out of time, the thing to change is the Dockerfile,
not the settings: order it so the slow, rarely-changing steps come first and are
cached, and copy your source in as late as possible.

## Starting your app

### The container started but your app did not answer {#app_not_answering}

```text
The container started but your app did not respond on the PORT environment variable. Please ensure your app listens on the port provided via the PORT environment variable.
```

**This is the one failure where the red panel and the build log say exactly the
same thing, word for word.** There is one sentence here, not two. Do not go
looking for a second, different one in the log.

**Read the first sentence, and disregard the second.** The first is true: your
container started and PivoCloud could not reach your app on the port it
declared. The second is wrong. **PivoCloud sets no `PORT` variable**, so there
is no value being provided for your app to listen on. The port PivoCloud probes
is the one your Dockerfile declares with `EXPOSE`.

That is a contract with a few sharp edges: which `EXPOSE` line is read, what
happens when there is no `EXPOSE` line at all, and why `ENV PORT` in the
Dockerfile makes things worse. All of it is on
[what your repository needs](/apps/deployment-contract).

This message is also emitted for almost any failure to start, not only for a
port mistake. If your `EXPOSE` line and your listening port already agree, your
app is crashing on boot instead, and the reason is in the container logs.

When your agent asks why the app is not working, the facts behind its answer
include `start_check`. `running` means PivoCloud recorded that your app was still
running but never answered. `not_recorded` means it recorded nothing about the
state of your app, for example on an attempt made before this was recorded.
`unavailable` means PivoCloud could not read that record just now, so the answer
is not certain, and asking again later may give a better one. If PivoCloud
recorded that your app had exited at the start check, the answer is
[the app exited right after it started](#crashed_on_start) instead of this one.

## What your AI agent can tell you about an app

If you connected an AI agent, you can ask it why an app is not working. It asks
PivoCloud and gets back one answer: a short sentence, how sure PivoCloud is, the
facts behind it, the next step to take, and a link to the entry on this page that
explains it. The answer is worked out from what PivoCloud recorded about your app.
Nothing is called live while the agent asks, and the answer never contains an env
var value or a line from your logs. How to connect an agent is on
[connecting your AI agent](/agents/connect).

Every answer also lists what PivoCloud could look at and what it could not. A
signal that could not be read is named in the answer, so an agent never takes
silence for a clean result. Logs and a live call to your app's address are not
looked at today, and are always in that list.

Many answers are explained under the failure they describe, higher on this page.
A build that failed is `build_failed`, under `The build itself failed`. A
container that started but did not give a good answer to the start check is
`app_not_answering`, under `The container started but your app did not answer`.
A repository PivoCloud could not clone is `clone_failed`, one it cannot read
is `repo_access_lost`, and a repository URL that is not valid is
`invalid_repo_url`. A missing Dockerfile is `dockerfile_missing`, a build that
ran out of time is `build_timeout`, and a deploy that a platform update
interrupted twice is `deploy_interrupted_by_platform`. Each of them sits under
the heading that explains that failure. The answers that have no failure
message of their own are explained below. When a deploy failed but the version
you had before is still running, the answer says so, and your app is still
serving visitors.

### The app is suspended because the wallet could not pay {#app_suspended_unpaid}

The renewal of this app could not be paid from your wallet, so PivoCloud
suspended it. This is the first thing an agent looks at, because a suspended app
explains everything else: it is not running, whatever its last deploy did.

**What to do:** top up your wallet in the console. Suspended databases come back
first, then apps, oldest first, as far as the balance goes. You do not need to
redeploy.

### The app was stopped from the console {#app_stopped}

Someone stopped this app from the console, so it is not running. A stopped app
does nothing on purpose: it is not a failure, and nothing is wrong with your code
or with PivoCloud. The answer gives the time it was stopped.

**What to do:** press `Start` on the app in the console. You do not need to
redeploy.

### The app has expired {#app_expired}

This app was set not to renew, and its paid period ended, so PivoCloud stopped
it. The answer gives the time the period ended. Your settings, variables and
plan are kept.

**What to do:** open the app's `Billing` tab in the console and turn the
`Auto-renewal` switch back on, and make sure your wallet can pay for the plan.
Then press `Redeploy`. Redeploying with the switch still off does not help: the
app runs again for a short time and then expires again, because it is still set
not to renew.

### A deploy is still running {#deploy_in_progress}

The latest deploy of this app has not finished, so the new version is not up
yet. The answer gives the stage the deploy is at and its id.

**What to do:** wait for the deploy to finish. You can also ask your agent to
follow that deploy and tell you when it ends.

When your agent follows a deploy, it is told its stage in plain words, how long
it has been running against the build limit, and when to ask again. The limit
covers the whole deploy, not only the build step. Until PivoCloud has recorded
the limit of the current attempt, the limit and the deadline are not shown.
Once a deploy is past its deadline the agent keeps asking at the normal pace
for that stage, rather than asking every second.

### The app has never been deployed {#never_deployed}

The app exists, but no deploy was ever started for it, so there is nothing
running. This is not a failure. An app that failed to deploy is not this: it
gets the answer for its failure.

**What to do:** press `Deploy` on the app in the console.

### The app exited right after it started {#crashed_on_start}

PivoCloud started your container and it stopped again straight away, before the
start check could succeed. The answer carries the exit code your app ended with.
The deployment log of that attempt has one line from the start check that says
whether the app was still running or had exited, and with which code.

**What to do:** open the app's logs in the console and read the last lines your
app printed before it stopped. Check the start command in your Dockerfile and
that your app has everything it needs to boot, then press `Redeploy`. An exit
code of 1 usually means your app raised an error. 137 usually means it was
stopped for using too much memory.

### The app keeps stopping and restarting {#crash_loop}

PivoCloud recorded that your app stops and starts again, repeatedly. It is up
for a moment and then gone, so a single look at it can show it as running.

**What to do:** open the app's logs in the console and find what makes it stop.
The cause is inside your app, so fix it there and press `Redeploy`.

### PivoCloud recorded the app as unhealthy {#app_down}

The app was deployed and was running, and the health check PivoCloud keeps on
it then recorded its container as not running. That is all the record says: it
does not say why the container is gone. A crash in your app can cause it, and so
can something outside your code, such as the container being stopped or a
restart of the machine it ran on. The answer gives the time of that check. It is
the last recorded check, not a call made at the moment you asked.

**What to do:** open the app's logs in the console to see whether the app exited
because of an error. If it did, fix that in your app and press `Redeploy`. If the
logs show nothing wrong, press `Redeploy` anyway, because the container may have
been stopped from outside your code. If a database is attached and not running,
the answer is about the database instead, because it is the cause to fix first.

### PivoCloud recorded a problem with the app's address {#address_not_configured}

The app is running and its health check shows no problem, but PivoCloud recorded a problem while setting up
its public address, so visitors may not reach it. The answer gives the status
PivoCloud recorded, and never the detail of the problem.

**What to do:** open the app in the console to check its address, then press
`Redeploy` so PivoCloud sets the address up again.

### The database attached to the app is not running {#database_unavailable}

The database attached to this app is not running, for example because it was
suspended when the wallet could not pay. An app that cannot reach its database
often looks unhealthy or restarts over and over, and the database is the cause
to fix first.

**What to do:** open the database in the console and see why it is not running.
Do not redeploy the app until the database runs, because a deploy made while the
database is stopped starts your app without the database's variables, and it
would fail again.

When a database is not running, your agent's list of env var names does not
include the names that database would add. They are listed again once it runs.

### PivoCloud found nothing wrong on its side {#healthy}

The last health check PivoCloud recorded found your app healthy. The answer gives
the time of that check. It is the last recorded check, not a call made at the
moment you asked, so it does not prove the app answers right now.

**What to do:** if the app still misbehaves, the cause is inside the app. Open
its logs in the console, and ask your agent to look at the code and the
configuration rather than at the platform.

### PivoCloud has no recorded cause {#unknown}

Nothing PivoCloud recorded explains the problem, so it does not guess. This is
also the honest answer for states that have no entry of their own on this page,
such as an app that is restarting or a failure PivoCloud does not recognise.

The answer names the facts it did find, for example the status of the app and of
its latest deploy, and lists every signal it could not read.

**What to do:** open the app in the console and read its deployment history and
its logs.

**When the cause is on PivoCloud's side.** A deploy can also fail because
PivoCloud did not give it something it needed, such as a public address, or
because PivoCloud's own build step could not start the build before your code
was built. The answer then says that the cause is on PivoCloud's side and that
nothing in your code needs to change. It still has the code `unknown`, and the
facts it lists include which failure it was. Contact PivoCloud support and give
them the deployment id from the answer. Do not expect `Redeploy` to help, because
a cause on PivoCloud's side can repeat on every attempt.

## A platform update interrupted your deploy {#deploy_interrupted_by_platform}

PivoCloud updates itself from time to time. If that happens while your deploy is
running, PivoCloud lets the deploy finish first whenever it can. When it cannot,
your deploy starts again from the beginning, once, on its own. It is the same
deploy, so it keeps its place in your deployment history and you do not need to
press anything.

If you saw this line in your build log, nothing is needed from you:

```text
A platform update interrupted this deploy. It starts again from the beginning (attempt 2 of 2). You do not need to do anything.
```

**Only if the update interrupts the deploy a second time in a row does it stop.**
The deploy then fails, and this sentence is its error message. You read it under
`Deployment Failed` on the `Deployment logs` tab of your app page. If your app
has no running version, the red panel at the top of the page also carries it in
its technical details. If the version you had before is still running, your app
stays running and no red panel appears. The sentence is not added to the log
lines below it.

```text
A platform update interrupted this deploy twice, so it was stopped. Your code did not cause this. Redeploy to try again.
```

**Your code did not cause this, and there is nothing to fix in it.** Do not
change your Dockerfile or your settings because of this message. Press
`Redeploy` and the deploy starts fresh.

The red panel does not offer a cause for this failure, and that is deliberate.
The panels for a clone, a Dockerfile or a build problem describe something you
can change, and none of them applies here.

What this does to your credit is in the next section.

## When Redeploy or the other buttons refuse

Pressing `Redeploy` can come back seven different ways. **This is the list for
that one button.** The other buttons on the app page have their own answers, and
this is not every response the platform can give you.

| What comes back | What it means | Do you meet it in the console? |
|---|---|---|
| `Not authenticated` | Your session has expired. Sign in again and retry. | Only with an expired session. |
| `Invalid app ID` | The app identifier in the request is not a valid one. | No. The console never builds a bad one, so this means something other than the console is calling. |
| `Repository validation failed` | The repository check refused the deploy. The useful sentence is the one beside it, and it is one of the eight under `Before the build starts`. | Yes. |
| `App not found` | The app is gone, usually deleted in another tab. Reload the page. | Yes. |
| `App is already deploying` | A deploy for this app is already running. | Yes, but you will not see it. |
| `A deployment is in progress for this app, retry in a moment` | Another change to this app is in flight. Wait a few seconds and press again. | Yes. |
| `Failed to trigger deployment` | The catch-all. Read the paragraph below before you assume it is a fault. | Yes. |

**The already-deploying answer is swallowed, so pressing `Redeploy` during a
deploy does nothing visible.** No message, no error, nothing changes on screen.
If `Redeploy` appears to do nothing at all, a deploy is already running: open the
`Deployments` tab and watch it there rather than pressing the button again.

**The same in-progress refusal is worded two ways, depending on which control
you pressed.** From `Redeploy` you get the sentence in the table. From `Stop`,
`Restart` or `Change plan` you get
`A deployment is in progress for this app. Try again in a moment.` instead. The
two sentences are the same condition and the same advice: wait a moment, then
try again.

`Start` is not in that list. The platform has no dedicated sentence for pressing
`Start` while a deploy is running, so do not go looking for one. If `Start`
comes back with a failure and a deploy is running, open the `Deployments` tab,
wait for that deploy to finish, then press `Start` again.

### A first deploy that fails with a trigger failure is usually about credit

`Failed to trigger deployment` is the sentence PivoCloud falls back to when it
has nothing more specific. One cause reaches it often enough to be worth naming,
and the sentence gives you no hint of it: **not enough credit in your wallet.**

An app with a monthly price is charged when it is first deployed, not when it is
created. If your wallet balance does not cover that charge, the deploy is
refused, no money moves, and what you read is the catch-all sentence. It is
about credit, not about your code, and there is nothing wrong with your
repository.

**What to do:** open your wallet, top it up, and press `Redeploy`.

This can only happen **before the app's first successful charge**. Once an app
has been charged, redeploying it takes no further payment, so a redeploy of a
running app can never fail for lack of credit. If you are seeing this on an app
that has already been billed, the cause is something else.

## What happens to your credit when a deploy fails

If a deploy fails **after** the charge has already gone through, PivoCloud
refunds it automatically and in full. You do not have to ask for it and there is
nothing to claim.

One consequence is worth knowing before you read your wallet history, because it
looks wrong and is not: **the next deploy that succeeds charges again.** The
refund cancels the month you paid for, so the paid month starts when your app
actually runs rather than when you first tried. A wallet showing a debit, then a
credit, then a second debit for the same app is one month paid for, not two.

**A deploy that a platform update interrupts twice follows the same rule, with
one difference.** If your previous version is still running when the deploy
stops, your app keeps running, and nothing is refunded and nothing is charged
again. If nothing was running, the deploy counts as a failed deploy and the
refund described above applies. The single automatic restart of an interrupted
deploy never charges you a second time.
