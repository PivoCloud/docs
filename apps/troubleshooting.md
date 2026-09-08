---
title: Why did my deploy fail?
description: "The messages PivoCloud shows when a deploy does not work, what each one really means, and what to change. Search this page for the exact sentence you were shown and it will take you to that failure."
last_verified: 2026-09-07
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

### PivoCloud could not clone your repository

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

**A badly formed URL lands in the second one.** If your repository URL is not
merely wrong but malformed, the clone fails in a way that is reported as a
network problem. So if you read the network sentence and your connection is
fine, read the repository URL character by character before you look at anything
else.

### The repository is private, or PivoCloud cannot read it

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

### The repository URL does not look valid

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

### PivoCloud could not find a Dockerfile to build

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

### The build itself failed

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

### The build ran out of time

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

### The container started but your app did not answer

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
