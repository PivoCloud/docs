---
title: Why did my deploy fail?
description: "The messages PivoCloud shows when a deploy does not work, what each one really means, and what to change. Search this page for the exact sentence you were shown and it will take you to that failure."
last_verified: 2026-10-30
---

## What the red panel shows

When a deploy fails, a red panel at the top of the app page explains what
happened. It holds two sentences and a link:

- the first sentence says what happened;
- the second says what to do next;
- the link, `What this means and what to do`, opens this page at the entry for
  that failure.

When the cause is on PivoCloud's side, or PivoCloud could not identify it, the
panel also shows a line that begins `Reference:`. That is a short code you give
to PivoCloud support, and it is explained under
`PivoCloud has no recorded cause`. A cause you can fix yourself, such as a
missing Dockerfile or a repository PivoCloud cannot read, carries no reference,
because support has nothing to add to what the panel already tells you.

The same explanation appears wherever the console reports the failure: on the
`Deployment logs` tab under `Deployment Failed`, and in the message under your
variables after a save that failed. A deploy that a newer push replaced is
cancelled rather than failed, and shows no red panel.

The build log underneath is the build's own output. PivoCloud shows it in full
and blanks only the values of your app's variables. It is the place to look for
what your own Dockerfile did, and this page does not try to explain it line by
line.

Between the lines of your own build output, PivoCloud writes a few plain steps of
its own: starting your app, checking that it answers on its port, and how the
deploy ended. Internal details never appear in those lines. The steps are quoted
under `What PivoCloud writes in your deploy log`, further down.

Every sentence the panel can show is quoted below, word for word, under the
failure it belongs to. Search this page for the sentence you were shown and it
takes you to the right entry.

## What is on this page, and what is not

This page is the index of the messages that **stop a deploy**: the ones that
leave your app undeployed and put a red panel on the app page, plus the ones
that refuse a deploy before it starts. It is an index of those. It is not an
index of everything PivoCloud can tell you.

The messages shown when another action on your app fails, such as saving
variables or stopping the app, are under
`When saving variables or another app action fails`.

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
is not connected yet. They appear in the card titled `Public URL` on the app's
`Domains` tab, not in the deployment logs. They are explained under
`When your app is running but its address is not ready`.

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

Two more sentences arrive in the same place.

```text
Could not reach GitHub. Check your connection and try again.
```

PivoCloud could not reach GitHub at all. Nothing is wrong with your repository
or your token. Wait a minute and press `Redeploy` again.

```text
Token rejected. Check that it has Contents: read-only access to this repository.
```

The token was read and GitHub refused it. Check it has not expired and that it
grants `Contents: read-only` on **this** repository, not on a different one.

## The clone

### PivoCloud could not clone your repository {#clone_failed}

The red panel reads:

```text
PivoCloud could not clone the app's repository, so the latest deploy did not start.
```

```text
Check the repository URL and name in the app settings, and if they are right, wait a minute and use Redeploy, because a clone can also fail on a temporary network problem.
```

Your repository does not have to be public. PivoCloud deploys private
repositories, and `The repository is private, or PivoCloud cannot read it` says
how.

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
`clone_failed`, and it lists which one it was. It gives the same advice as the
panel: check the repository URL and name, and if they are right, wait a minute
and press `Redeploy`.

**A badly formed URL lands in the second one.** If your repository URL is not
merely wrong but malformed, the clone fails in a way that is reported as a
network problem. So if you read the network sentence and your connection is
fine, read the repository URL character by character before you look at anything
else.

### The repository is private, or PivoCloud cannot read it {#repo_access_lost}

The red panel reads:

```text
PivoCloud cannot read the app's repository, so the latest deploy did not start.
```

```text
Fix the repository access in the app settings, by reconnecting the GitHub App or attaching a valid token, then use Redeploy.
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
The app's repository URL is not a valid GitHub address, so the latest deploy did not start.
```

```text
Correct the repository URL in the app settings, then use Redeploy.
```

What PivoCloud could not do is read the URL as a GitHub HTTPS address. Your
repository does not have to be public.

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
PivoCloud could not use a Dockerfile at the path the app is set to build, so the build never started.
```

```text
Add a Dockerfile at that path or correct the Dockerfile path in the app settings, then use Redeploy.
```

The build log names the path itself, with your own paths in it, and this is the
version worth reading. It begins with `No Dockerfile at ` followed by the file
it looked for, then ` (build context: ` followed by the directory it looked
inside, so a whole instance reads like
*"No Dockerfile at `<path>` (build context: `<dir>`)."*

There is a second form of this failure. If the path you gave points at something
that is not a file, a directory of that name for example, the log says
*"Dockerfile path `<path>` is not a regular file (build context: `<dir>`)."*
instead. Same cause, different mistake: the first means nothing is there, the
second means something is there and it cannot be built.

PivoCloud does not require a Dockerfile at your repository root. What failed is
the path it actually resolved, which is the one printed in the log.

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

PivoCloud also records this sentence against the deploy with
`docker build failed: ` in front of it, so if support quotes it that way,
everything above still applies.

**What to do:** fix whichever of the two settings is wrong, then redeploy. The
button on the app page reads `Redeploy`. Nothing else needs changing and you do
not need to push a commit for the new settings to take effect.

### The build itself failed {#build_failed}

The red panel reads:

```text
The latest build of this app failed, so the new version never started.
```

```text
Read the build logs in the console, fix what stops the build, then use Redeploy.
```

**This panel stays general on purpose.** The build ran and something inside your
own Dockerfile failed: a package that would not install, a compile error, a
command that returned non-zero. PivoCloud has no opinion about what your build
does, so it has nothing to add. Read the build log from the bottom upwards. The
last command that ran before the failure is the one to look at.

If your repository uses symlinks, special files or a `.dockerignore`, read
[Can my repository contain symlinks?](/apps/symlinks) for how PivoCloud treats
them during the build.

**One build message is yours to fix and lands here too.** When PivoCloud cannot
use your `.dockerignore` file, the build log shows this sentence, and PivoCloud
records it with `docker build failed: ` in front of it:

```text
failed to create build context: PivoCloud could not use your .dockerignore file. Check that each line is a valid pattern, fix it, and redeploy.
```

Open that file, check that every line is a valid pattern, fix the line that is
not, and redeploy.

The other build messages, about starting the build or sending your code to it,
are on PivoCloud's side, and the panel treats them that way. They are under
`PivoCloud has no recorded cause`, with the advice to redeploy once.

**If your app has no Dockerfile.** PivoCloud builds some apps without a Dockerfile.
Only some machines do this for now, so you may never see what follows. On such a
build, the first line of the build log reads `Building with Railpack`, then the
version PivoCloud uses, then `, because this app has no Dockerfile in its root directory.`

If the build log ends with one of these two sentences, PivoCloud could not tell how
to build or start your app:

```text
PivoCloud could not tell what language or framework this app uses, so it cannot build it without a Dockerfile. Add a Dockerfile, or add a start command in a Procfile or in railpack.json. See https://docs.pivocloud.com/apps/deploy-without-a-dockerfile
```

```text
PivoCloud recognised this app but could not find how to start it. Add a start command in a Procfile or in railpack.json, or add a Dockerfile. See https://docs.pivocloud.com/apps/deploy-without-a-dockerfile
```

A third sentence says your `railpack.json` asks for build secrets, which PivoCloud
does not support:

```text
Your railpack.json lists build secrets, and PivoCloud does not support build secrets. Remove them from railpack.json, or add a Dockerfile, then redeploy.
```

For all three the red panel reads:

```text
PivoCloud could not build this app without a Dockerfile, because it could not tell how to build or start it.
```

```text
Read the last line of the build logs. Add a Dockerfile, or a start command in a Procfile or in railpack.json, then use Redeploy.
```

Two more sentences are on PivoCloud's side, not yours, and your code does not need
to change. Redeploy once. If the same sentence comes back, contact PivoCloud
support:

```text
PivoCloud could not analyse your repository because of a temporary problem on its side. Redeploy to try again. If the same message comes back, contact PivoCloud support.
```

```text
PivoCloud cannot build an app without a Dockerfile on this machine right now. This is on PivoCloud's side. Redeploy to try again. If the same message comes back, contact PivoCloud support.
```

### The build ran out of time {#build_timeout}

The red panel names the limit your build was held to:

```text
The latest build ran out of time after 30 minutes, so the new version never started.
```

```text
Use Redeploy in the console to continue, because the build restarts from the layers it already built.
```

When PivoCloud cannot read the limit, the first sentence leaves the number out:

```text
The latest build ran out of time, so the new version never started.
```

The build log carries the same fact. It writes the limit as `30m0s`, which means
30 minutes:

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

The red panel reads:

```text
The app started, but it did not give a successful answer to PivoCloud's start check.
```

```text
Open the app's logs in the console to see why it did not answer the start check, then use Redeploy.
```

The build log can carry a longer sentence written by the build step:

```text
The container started but your app did not respond on its port. PivoCloud sets no PORT variable. It probes the port in the first EXPOSE line of your Dockerfile, so make sure your app listens on that port on all interfaces (0.0.0.0).
```

The port PivoCloud probes is the one your Dockerfile declares with `EXPOSE`.
Your container started and PivoCloud could not reach your app on that port.

**What counts as started.** PivoCloud asks your app for its home page (`/`), using
your app's own public address as the host name, and it does not follow redirects.
Any answer below 500 counts as started: a page, a redirect, a login screen, even a
404 or a 401. An answer of 500 or more, or no answer at all, does not. So a
Django or Laravel app that refuses unknown host names is judged on the answer a
visitor gets, and an app that redirects every request to `https` is judged on the
redirect.

**When the deploy goes live on a 4xx answer.** If your home page answers with a
status from 400 to 499, PivoCloud puts the deploy live and writes one warning line
in the deploy log, so a page that is missing is not silent. The numbers in the
examples on this page are examples; your log shows your own app's values.

```text
Your app answered with HTTP 404 on its home page (/). PivoCloud put it live, because only an answer of 500 or more stops a deploy. Check that your app serves its pages.
```

If the 404 is not what you meant, check that your app serves a page at `/`, then
redeploy.

**When the deploy fails, the last line says why.** The build log ends with one of
these sentences, each naming what PivoCloud saw. The first three are for every app.
They carry numbers that are examples here.

If your app answered with an error status, the app is running and listening, and
the fix is in the app, not in its port:

```text
Your app answered with HTTP 500 on port 8080, so it is listening, but it returned an error. Open the app's logs to find the error, fix it, then redeploy.
```

If your app stopped before it answered, the problem is in how it starts:

```text
Your app stopped with exit code 137 before it answered, so the problem is in how it starts, not in its port. Open the app's logs to see why it stopped, then redeploy.
```

If your app is listening, but on a port other than the one PivoCloud asked on, the
sentence names both. For an app built from a Dockerfile it points at the first
`EXPOSE` line:

```text
Your app did not answer on port 8080, but it is listening on port 3000. Change the first EXPOSE line of your Dockerfile to 3000, or make your app listen on port 3000, then redeploy.
```

When PivoCloud saw no listening port at all for a Dockerfile app, you get the long
sentence quoted above.

**If you see a sentence about the Listen port or the PORT variable.** Only some
machines build apps that have no Dockerfile, so you may never see these two. They
are the same answers for such an app, which has a Listen port setting instead of an
`EXPOSE` line:

```text
Your app did not answer on port 8080, but it is listening on port 3000. Set Listen port to 3000 in the app's settings, or make your app listen on the PORT variable, then redeploy.
```

```text
Your app started but did not answer on port 8080. Make sure it listens on all interfaces (0.0.0.0), on the port in the PORT variable, then redeploy.
```

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

### If you see a line about APP_KEY in the deploy log of a Laravel app

A Laravel app cannot start without an `APP_KEY` variable. When your Laravel app
has none, PivoCloud makes one the first time the app is deployed and saves it in the
app's variables, so you do not have to type it. The deploy log tells you what
happened, in one of three lines.

```text
PivoCloud created an APP_KEY for this Laravel app and saved it in the app's variables. It will not change on later deploys.
```

This is the normal first deploy. You do not have to do anything. You can see the
variable in the app's variables, and the history shows that PivoCloud wrote it.
Laravel uses the key to encrypt sessions and other saved data, so keep it. If you
want your own key, set `APP_KEY` yourself and PivoCloud will never replace it.

```text
This Laravel app has no APP_KEY. PivoCloud created one before and does not create a second, because a new key makes data saved with the old one unreadable. Add APP_KEY back in the app's variables.
```

Someone deleted the `APP_KEY` variable after PivoCloud made it. PivoCloud does not
make a second key, because anything saved with the first one would stop working.
Add `APP_KEY` back in the app's variables with the value you had, then redeploy.
If you have no copy of it, set a new key yourself, knowing that sessions and data
encrypted with the old key cannot be read again.

```text
PivoCloud could not create an APP_KEY for this Laravel app during this deploy. Add APP_KEY in the app's variables, or redeploy later.
```

PivoCloud could not make the key this time, and the deploy went on without it. A
Laravel app with no key answers with an error, so the start check may fail after
this line. Redeploy to try again, or add `APP_KEY` in the app's variables yourself.

## What PivoCloud writes in your deploy log

Between the lines of your own build output, the deploy log holds a few short lines
that PivoCloud writes itself. They say what is happening to your app: it is
starting, PivoCloud is checking that it answers, and how the deploy ended. They are
plain on purpose. They never show PivoCloud's internal steps, and they never repeat
or change your own build output.

The examples below use port 3000, attempt 3 with 40 seconds left, HTTP status 500,
the address `my-app.pivocloud.app` and 3 variables. Your log shows your own app's
values in the same places.

**The steps of a deploy.** PivoCloud starts your app, then checks that it answers
on the port your Dockerfile declares with `EXPOSE`:

```text
Starting your app.
```

```text
Checking that your app answers on port 3000.
```

```text
Your app answers on port 3000.
```

When PivoCloud cannot tell which port that is, the two lines about the check read
without a port:

```text
Checking that your app answers.
```

```text
Your app answers.
```

**When your app has not answered yet.** PivoCloud checks again and again until the
time it waits runs out. Each time your app has not given a successful answer, the
log gets one of two lines. Each carries the number of the attempt and the seconds
PivoCloud still waits, and your app's own port when PivoCloud can tell which port
that is.

The first means nothing answered on that port yet:

```text
Your app did not answer on port 3000 yet (attempt 3, 40 s left).
```

The second means your app did answer, but with an error status, a status of 500 or
more. Any answer below 500 counts as started:

```text
Your app answered on port 3000 with HTTP status 500 instead of a success status (attempt 3, 40 s left).
```

When PivoCloud cannot tell which port that is, the same two lines read without a
port:

```text
Your app did not answer yet (attempt 3, 40 s left).
```

```text
Your app answered with HTTP status 500 instead of a success status (attempt 3, 40 s left).
```

These lines are normal while a slow app is still starting, so one of them alone is
not a problem. If the last attempt fails too, the deploy fails with the answer under
`The container started but your app did not answer`. The place to look is your
app's own logs and its environment variables: a port that does not match the
`EXPOSE` line, or a variable your app needs and does not have, are the usual
causes.

**When your variables could not be loaded.** If PivoCloud cannot read your app's
environment variables at the moment of a deploy, it still starts your app, and says
so:

```text
We could not load your environment variables, so your app started without them. Deploy again to retry.
```

Your variables are not lost. Deploy again, and PivoCloud reads them again.

**How the deploy ended.** One of these three lines closes a deploy. Your app is
running and its address is connected:

```text
Your app is live at https://my-app.pivocloud.app.
```

Your app is running, but its address is not connected yet. The Public URL card on
the app's Domains tab says what happens next, and the section
`When your app is running but its address is not ready` on this page explains it:

```text
Your app is running, but its public address is not ready yet. The Public URL card on the app's Domains tab says what happens next.
```

The deploy did not finish. The red panel at the top of the app page says what
happened and what to do, and the section `What the red panel shows` at the top of
this page explains the panel:

```text
This deploy did not finish. The red panel on the app page says what happened and what to do.
```

**When you change your environment variables.** Saving variables starts your app
again with them. The log says how many it starts with, and then how it went:

```text
Starting your app again with 3 environment variables.
```

```text
Starting your app again with 1 environment variable.
```

```text
Your environment variables are applied and your app is running with them.
```

```text
Your environment variables were not applied. The app page says what happened and what to do.
```

**When your app is started again without a change.** Starting an app that was
stopped, resuming one that was suspended, and changing the plan all start your app
again without touching its variables, so the log does not mention them:

```text
Starting your app again.
```

```text
Your app is running again.
```

```text
Your app could not be started again. The app page says what happened and what to do.
```

When you return an app to an earlier version, the log shows three lines of its
own. The first says the return has begun, the second says the app is running the
version you chose, and the third says the app could not be returned and is still
running as before:

```text
Returning your app to the version you chose.
```

```text
Your app is running the version you chose.
```

```text
Your app could not be returned to that version. It is still running as before, and the app page says what happened.
```

**When some of these lines are hidden.** Some deploys show one line in place of
PivoCloud's own steps: deploys that were run before these plain lines existed, and
deploys that ran while PivoCloud was being updated. Your own build output is never
hidden by it:

```text
Some PivoCloud step messages for this deploy are hidden. Your own build output is not affected.
```

## When another action on your app fails

Saving variables, stopping or starting the app, changing its address, setting up
custom domains and deleting it all run in the background after you press the
button. When one of them fails, the console shows the same kind of explanation
as for a deploy: two sentences and a link to this entry, and a reference when
the cause is on PivoCloud's side or could not be identified.

That reference is the same one a failed deploy shows. What it is and what to send
with it are explained under `PivoCloud has no recorded cause`.

### When saving variables or another app action fails {#app_action_failed}

Your variables are saved first and applied to the running app afterwards, so
most of the messages below begin `Your variables are saved`. That first sentence
is good news: nothing you typed was lost. What failed is the step that makes the
running app use them.

#### After you save variables

**The new container did not become healthy.**

```text
Your variables are saved, but the new container did not become healthy.
```

```text
Your app was rolled back and is serving its previous configuration. Check your app logs, then save again.
```

The new container started with your new variables but did not pass PivoCloud's health check in time. PivoCloud put your previous container back, so visitors still reach your app. Look at your app logs for why it does not start with the new values, fix that, and save again.

**The new container could not be started.**

```text
Your variables are saved, but a new container could not be started.
```

```text
Nothing about your app changed. If this app has never been deployed successfully, there is no image to start yet: check its Git connection (a private repository needs an access token), then press Deploy (or Redeploy) on the app page.
```

The most common cause is an app that has never deployed successfully, so there is no built version to start with the new variables yet. Fix the repository connection if it needs it, then press `Deploy` or `Redeploy`. Your variables will be used by that deploy.

**PivoCloud could not read your variables back.**

```text
Your variables are saved, but the platform could not read them back to apply them.
```

```text
Nothing about your app changed. Try again. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

Nothing about your app changed. The cause is on PivoCloud's side. Try again, and if it fails again, give support the reference.

**PivoCloud could not read which port your running app uses.**

```text
Your variables are saved, but the platform could not read which port your running app uses.
```

```text
Nothing about your app changed. Try again. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

Nothing about your app changed. The cause is on PivoCloud's side. Try again, and if it fails again, give support the reference.

**PivoCloud could not resolve the app's address.**

```text
Your variables are saved, but the platform could not resolve this app's address.
```

```text
Nothing about your app needs to change. Contact PivoCloud support and give them the reference shown with this message.
```

This is a problem on PivoCloud's side and it repeats on every attempt, so saving again will not help. Give support the reference.

**PivoCloud could not run the apply step.**

```text
Your variables are saved, but the platform could not run the apply step.
```

```text
This is a platform configuration problem, not a problem with your variables. Contact PivoCloud support and give them the reference shown with this message.
```

This is a platform configuration problem, not a problem with your variables. Give support the reference.

**Traffic did not switch over to the new container.**

```text
Your variables are saved, but traffic did not switch over to the new container.
```

```text
Your app is still reachable on its previous container. Try again. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

Your app is still reachable on its previous container, with its previous variables. Save again, and if it fails again, give support the reference.

**Your app did not start listening in time.**

```text
Your variables are saved, but your app did not start listening on a port in time.
```

```text
Your app was left as it was. PivoCloud sets no PORT variable. Check that your app listens on the port its Dockerfile declares with EXPOSE, on all interfaces, then save again.
```

[What your repository needs](/apps/deployment-contract) explains how the port is chosen.

**Another apply was already running.**

```text
Your variables are saved. Another apply was already in progress.
```

```text
Wait for that apply to finish, then check your variables.
```

Two saves overlapped. Wait for the first to finish, then check that your variables are what you expect.

**The apply did not complete, for a reason PivoCloud did not name.**

```text
Your variables are saved, but the apply did not complete.
```

```text
Contact PivoCloud support and give them the reference shown with this message.
```

Give support the reference. It lets them find what happened without asking you to describe it.

#### When undoing a change fails

These come from `Undo` in `Recent changes`, below the variables editor. They are
shown in the undo dialog, except the last one, which is a short notice on the page.
Variables are only changed when the undo is confirmed, so none of these leaves
the variables half changed.

If the variables changed while the dialog was open, for example from another tab, the
dialog does not fail. It reads the up-to-date list again and shows it with a notice. Check
the list, then confirm again. Nothing was written by the first click.

**A deploy or another change is running.**

`A deploy or another change is running for this app. Try again when it finishes.`

The app is busy with a deploy, a restart or another change to its variables, possibly
one an agent made. Wait for it to finish, then undo again.

**PivoCloud could not check what the undo would change.**

`We could not check what this undo would change. Close this and try again.`

The dialog could not read the preview, so it shows no list and `Undo change` stays off.
Close it and open it again. Nothing was changed.

**PivoCloud refused the undo.**

`We could not undo this change. Nothing was changed. Try again.`

The request reached PivoCloud and was refused, so your variables are as they were. Try
again. If it fails again, reload the page and check the entry in `Recent changes`.

**PivoCloud could not confirm the undo.**

`We could not confirm the undo. Reload the page to see the current variables.`

The request may have been applied, but the answer did not reach your browser, for example
because your connection dropped. This message does not say that nothing changed, because
that may not be true. Reload the page and read `Recent changes`: a new entry
`Undone by you` means the undo happened. If there is none, undo again.

**Your browser sent the undo from an address PivoCloud does not accept.**

The undo dialog can say that your browser sent the request from an address PivoCloud does
not accept, and that nothing changed. The exact sentence is on
[How do I see, change and cut off my AI agents?](/agents/manage).

Nothing was changed. Open the console from its normal address, reload the page, and
undo again.

**The change can no longer be undone.**

`This change can no longer be undone.`

The entry was among the 20 most recent changes when the page loaded, and newer changes
have pushed it out since. The page reads the list again. Variables are not lost: only the
way back to that older state is gone.

#### Stopping, starting, moving or deleting the app

**Deleting the app.**

```text
PivoCloud could not finish deleting this app.
```

```text
Contact PivoCloud support and give them the reference shown with this message.
```

The deletion started and did not finish. Give support the reference so they can finish it.

**Setting up custom domains.**

```text
PivoCloud could not finish setting up this app's custom domains.
```

```text
Open the app's Domains tab to check them, then try again. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

Open the `Domains` tab to check your domains, then try again. If it fails again, give support the reference.

**Moving the app to a new address.**

```text
PivoCloud could not move this app to its new address.
```

```text
Try the change again. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

Try the change again, and if it fails again, give support the reference.

**Starting the app again.**

```text
PivoCloud could not start this app again with its current settings.
```

```text
Try again. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

Check the app's state in the console and try again. If it fails again, give support the reference.

**Stopping the app.**

```text
PivoCloud could not stop this app.
```

```text
Try Stop again. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

Try `Stop` again, and if it fails again, give support the reference.

#### After you go back to an earlier version

**The earlier version could not be restored.**

```text
PivoCloud could not return this app to that version.
```

```text
Try again. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

Try the version again, and if it fails again, give support the reference.

**The earlier version is no longer kept.**

```text
This version is no longer kept, so it could not be restored.
```

```text
Your app was left as it was. Pick a version that is listed, or push or redeploy to run your code again.
```

PivoCloud keeps only a few recent versions of your app, and this one was removed. It will not be offered again. Pick another version from the list, or deploy your code again.

**The earlier version could not be verified.**

```text
This version could not be verified, so your app was left as it was.
```

```text
Try again. If it happens again, contact PivoCloud support and give them the reference shown with this message.
```

Your app is unchanged. Try again, and if it happens again, give support the reference.

**PivoCloud could not read the app's version right now.**

```text
PivoCloud could not read this app's version right now.
```

```text
Your app was left as it was. Try again in a minute. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

This says nothing about your code, so you do not need to deploy again. It can also appear after you save variables or start the app again. Wait a minute and try once more, and if it fails again, give support the reference.

**The app has no version to start from.**

```text
Your app has no version to start from.
```

```text
Deploy it again.
```

The app was never deployed successfully, or its built version was removed. Press `Deploy` to build it again.

#### When your own deploy is queued {#deploy_queued}

A deploy of your app can wait for a build slot and show `Queued`. It starts on its
own, and nothing needs fixing. While it waits, the actions below are unavailable, and the
console explains why with a sentence that starts `This app has a deploy queued.` and names what you tried.
Every one means the same: the deploy runs by itself, and the action is available
once it has run.

Stopping the app.

`This app has a deploy queued. You can stop it once that deploy has run.`

Starting the app.

`This app has a deploy queued. You can start it once that deploy has run.`

Restarting the app.

`This app has a deploy queued. You can restart it once that deploy has run.`

Changing the app's plan.

`This app has a deploy queued. You can change its plan once that deploy has run.`

Rolling the app back.

`This app has a deploy queued. You can roll it back once that deploy has run.`

Returning to the version you rolled back from.

`This app has a deploy queued. You can return to this version once that deploy has run.`

Saving variables.

`This app has a deploy queued. You can save its variables once that deploy has run.`

The `More actions` menu carries the shorter form,
`This app has a deploy queued. More actions are available once that deploy has run.`
Nothing you typed is lost while you wait. Open the `Deployments` tab to watch the
queued deploy start.

#### When the cause is on PivoCloud's side

These four apply to any of the actions above.

**A problem on PivoCloud's side that repeats.**

```text
This action on your app did not complete because of a problem on PivoCloud's side.
```

```text
Nothing about your app needs to change. Contact PivoCloud support and give them the reference shown with this message.
```

Do not try again: the cause is on PivoCloud's side and a retry will fail the same way. Give support the reference.

**A reason PivoCloud could not identify.**

```text
This action on your app did not complete for a reason PivoCloud could not identify.
```

```text
Contact PivoCloud support and give them the reference shown with this message.
```

Give support the reference.

**A platform update interrupted the action twice.**

```text
A platform update interrupted this action on your app twice, so it was stopped, and your app did not cause this.
```

```text
Nothing about your app needs to change. If the console still offers this action, do it again. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

PivoCloud updates itself from time to time and restarts an interrupted action once on its own. When the update interrupts it a second time, the action is stopped. Your app did not cause this. Do the action again if the console still offers it, and if it fails again, give support the reference.

**A platform recovery interrupted the action.**

```text
A platform recovery interrupted this action on your app, and your app did not cause this.
```

```text
Check your app's current state in the console. If the console still offers this action, do it again. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

Sometimes PivoCloud has to recover its own systems. An action that was in progress at that moment is set aside, and the message cannot say how far it got. Look at your app in the console to see where it stands, do the action again if the console still offers it, and if it fails again, give support the reference.

**PivoCloud lost track of the action.**

```text
PivoCloud lost track of this action on your app and ended it, and your app did not cause this.
```

```text
Check your app's current state in the console. If the console still offers this action, do it again. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

PivoCloud waits a set time for an action to report back. When nothing arrives in that time, it ends the action so your app is no longer blocked and you can act on it again. It cannot say how far the action got before it went quiet, so look at your app in the console first, do the action again if the console still offers it, and if it fails again, give support the reference.

## When your app is running but its address is not ready {#public_address}

After a deploy, PivoCloud connects your app to its public address. Your app can be
running while that step has not finished. The card titled `Public URL` on the
app's Domains tab shows where this stands. It is a card on the Domains tab,
not on the Overview tab.

**A yellow message** means the address is still being set up. Your app is running and
nothing is wrong. No reference is shown, because there is nothing for PivoCloud
support to look up. The message reads:

```text
Your app is running. Its secure address is still being set up.
```

With this next step:

```text
No action is needed from you.
```

**When the yellow message goes away.** The message records how your last deploy left
the address, and PivoCloud does not clear it by itself. It stays until your next
successful deploy replaces it, even if the secure address starts working before
then. If you want the message to show the current state, deploy your app again.

**A red message** means PivoCloud could not connect your app to its public address.
Your app is still running. The cause is on PivoCloud's side, not in your code, so
nothing in your repository needs to change. PivoCloud tries again by itself. The
message reads:

```text
Your app is running, but we could not connect it to its public address yet.
```

With this next step:

```text
This is on our side and we retry automatically. If it still fails in 10 minutes, contact PivoCloud support and give them the reference shown with this message.
```

**When the red message goes away.** Once your app is connected to its public address,
PivoCloud notices on its own and the red message clears, usually within a minute. You
do not need to do anything. There are two cases where it does not clear. One is a
deploy that changed your app's address and could not update the connection while it
ran. PivoCloud does not replace a connection that already exists, so it leaves that
one as it is and the red message stays. The other is a deploy that changed your
app's port while PivoCloud could not reach the part of its own system that holds
your address, so it could neither update the connection nor remove the old one. In
both cases, deploy your app again, and the deploy puts the connection right.

**The reference** that starts with `a-` is shown with the red message only. It
identifies your app for PivoCloud support, so give it to them when you write. It
is not shown with the yellow message, because the yellow message needs no support.

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

A crash your app recovers from by itself is recorded too. When your app's process
exits and PivoCloud starts it again straight away, the app's health history
(click the status badge on the app page) shows Container exited unexpectedly at
the time of the crash, then Container recovered after the next health check,
usually within a minute.

**What to do:** open the app's logs in the console to see whether the app exited
because of an error. If it did, fix that in your app and press `Redeploy`. If the
logs show nothing wrong, press `Redeploy` anyway, because the container may have
been stopped from outside your code. If a database is attached and not running,
the answer is about the database instead, because it is the cause to fix first.

### PivoCloud recorded a problem with the app's address {#address_not_configured}

The app is running and its health check shows no problem, but PivoCloud recorded that its public address is
not ready: a problem for the red status, still being set up for the yellow one. Visitors may not reach it yet. The answer gives the status
PivoCloud recorded, and never the detail of the problem.

**What to do:** it depends on the status PivoCloud recorded. For the red status,
PivoCloud retries the address automatically. If it is still not working after 10
minutes, contact PivoCloud support. For the yellow status, no action is needed,
and the answer says the address is still being set up. PivoCloud does not clear
the yellow status by itself: if you want it to show the current state, deploy your
app again, because the next successful deploy replaces it. What the address panel
shows, and when its messages go away, is under
`When your app is running but its address is not ready`.

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

**The reference.** When the cause is on PivoCloud's side, or PivoCloud could not
identify it, the red panel carries a line that begins `Reference:`, and your
agent's answer names the same code. It looks like `d-` followed by 8 letters and
digits, for example `d-3f9a1c2e`. It points at the one deploy or action that failed, so
support can find it without asking you to describe it. A cause you can fix
yourself, such as a missing Dockerfile, has no reference. A deploy that a
platform update interrupted twice carries one.

When you contact PivoCloud support, send three things: the reference, the name of
your app, and roughly when the failure happened. You do not need to copy
anything from the build log. The reference gives nobody access to your app. It
only tells support which deploy to look at.

#### What the red panel says for a deploy that lands here

Four panels share this entry. Read the first sentence to find yours.

**A reason PivoCloud could not identify.** The deploy failed with text that
PivoCloud does not recognise, so it does not guess a cause:

```text
This deploy failed for a reason PivoCloud could not identify.
```

```text
Redeploy to try again. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

Redeploy once. If the same message comes back, contact PivoCloud support with the
reference.

**A problem on PivoCloud's side that repeats on every attempt.** PivoCloud did
not give the deploy something it needed, such as a public address. Redeploying
will not help:

```text
The latest deploy failed because of a problem on PivoCloud's side, and nothing in your code caused it.
```

```text
Nothing in your code needs to change, and this cause repeats on every attempt. Contact PivoCloud support and give them the reference shown with this message.
```

Nothing in your code needs to change. Contact PivoCloud support with the
reference.

**A problem on PivoCloud's side that may pass.** PivoCloud's own build step
could not start the build, or could not send your code to it, before your code
was built. The build log shows one of these sentences, and PivoCloud records each
with `docker build failed: ` in front of it. The panel shows the same two
sentences for all of them:

```text
The latest deploy failed because of a problem on PivoCloud's side, and nothing in your code caused it.
```

```text
Nothing in your code needs to change. Redeploy once. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

PivoCloud could not hand your code to the build:

```text
docker build failed: failed to start docker build: PivoCloud could not start the build of your code. Redeploy to try again. If the same message comes back, contact PivoCloud support.
```

PivoCloud could not package your repository for the build:

```text
docker build failed: failed to create build context: PivoCloud could not package your repository for the build. Redeploy to try again. If the same message comes back, contact PivoCloud support.
```

A file in your repository changed while PivoCloud was packaging it:

```text
docker build failed: failed to create build context: A file in your repository changed while PivoCloud was packaging it for the build. Redeploy to try again.
```

The build lost its connection to the PivoCloud build service:

```text
docker build failed: The Docker build session failed during this build: The build lost its connection to the PivoCloud build service. Redeploy to try again. If the same message comes back, contact PivoCloud support.
```

The connection to the build was lost while its output was being read:

```text
docker build failed: failed to start docker build: The connection to the build was lost while its output was being read. Redeploy to try again. If the same message comes back, contact PivoCloud support.
```

PivoCloud could not open the connection it needs to fetch your base image:

```text
docker build failed: Could not open the Docker build session, so the build cannot resolve its base image. Redeploy to try again. If the same message comes back, contact PivoCloud support.
```

PivoCloud could not set up the build of your code because of a fault on its side:

```text
docker build failed: failed to start docker build: PivoCloud could not set up the build of your code because of a fault on our side. Your Dockerfile is not the cause. Redeploy to try again. If the same message comes back, contact PivoCloud support.
```

**What to do:** redeploy once. If any of these messages comes back on the second
try, contact PivoCloud support and give them the reference shown with the panel.

**A deploy that a platform recovery interrupted.** Sometimes PivoCloud has to
recover its own systems. A deploy that was in progress at that moment is set
aside, and the panel cannot say how far it got:

```text
A platform recovery interrupted this deploy, and your code did not cause this.
```

```text
Check your app's current state in the console, then deploy again. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

Look at the app in the console to see which version is running. If it is not the
one you expect, deploy again. If that fails too, contact PivoCloud support with
the reference.

**A deploy that PivoCloud lost track of.** PivoCloud waits a set time for a
deploy to report back. When nothing arrives in that time, it ends the deploy so
your app is no longer blocked, and the panel cannot say how far the deploy got:

```text
PivoCloud lost track of this deploy and ended it, and your code did not cause this.
```

```text
Check your app's current state in the console, then deploy again. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

Look at the app in the console to see which version is running. If it is not the
one you expect, deploy again. If that fails too, contact PivoCloud support with
the reference.

**A deploy whose details PivoCloud hid.** PivoCloud blanks your app's values (for
example the values of your environment variables) out of everything it shows you
about a deploy. When it could not load those values, it cannot tell which words
to blank, so it hides the whole message instead. The panel then falls back to
the first case above, a reason PivoCloud could not identify. PivoCloud records
this sentence for the failed deploy:

```text
This deploy failed, and its details are hidden because PivoCloud could not load this app's values to blank them. Deploy again to see the reason.
```

And a line in the deploy log reads:

```text
This deploy message is hidden because PivoCloud could not load this app's values to blank them. Deploy again to see it.
```

Nothing is wrong with your code because of these two messages. The details were
hidden because PivoCloud could not load the app's values to blank them, so deploy
again to see the reason.

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
The deploy then fails, and the red panel reads:

```text
A platform update interrupted the latest deploy twice, so it was stopped, and your code did not cause this.
```

```text
Use Redeploy in the console to try again, because nothing in your code needs to change. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

The panel carries a reference, because the cause is on PivoCloud's side. If
your app has no running version, the panel is at the top of the page. If the
version you had before is still running, your app stays running and no red panel
appears at the top of the page, and the same explanation is under
`Deployment Failed` on the `Deployment logs` tab. PivoCloud also records this
sentence for the deploy:

```text
A platform update interrupted this deploy twice, so it was stopped. Your code did not cause this. Redeploy to try again.
```

**Your code did not cause this, and there is nothing to fix in it.** Do not
change your Dockerfile or your settings because of this message. Press
`Redeploy` and the deploy starts fresh. If it fails again, contact PivoCloud
support and give them the reference.

What this does to your credit is in the next section.

## When Redeploy or the other buttons refuse

Pressing `Redeploy` can come back eight different ways. **This is the list for
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
| `Your wallet balance is too low` | Your wallet does not cover the charge for the action you pressed. The sentence beside the title tells you to top up, then try again. Nothing was charged. See [when an action is refused for credit](/billing/credit-and-charges#when-an-action-is-refused-for-credit). | Yes, as a notice with the missing amount and a Top up button. |
| `Failed to trigger deployment` | The catch-all. It no longer stands in for an empty wallet. Read the section below before you assume it is a fault. | Yes. |

**The already-deploying answer is swallowed, so pressing `Redeploy` during a
deploy does nothing visible.** No message, no error, nothing changes on screen.
If `Redeploy` appears to do nothing at all, a deploy is already running: open the
`Deployments` tab and watch it there rather than pressing the button again.

**The same in-progress refusal is worded two ways, depending on which control
you pressed.** From `Redeploy` you get the sentence in the table. From `Stop`,
`Start`, `Restart` or `Change plan` you get
`A deployment is in progress for this app. Try again in a moment.` instead. The
two sentences are the same condition and the same advice: wait a moment, then
try again.

`Start` answers the in-progress sentence too, while a deploy, a restart or a stop
is still running for the app. Wait for that job to finish (the `Deployments` tab
shows a deploy), then press `Start` again.

### An empty wallet now has its own message

`Failed to trigger deployment` is the sentence PivoCloud falls back to when it
has nothing more specific. It no longer means a credit problem. When your wallet
does not cover the charge for a deploy, the console now says
`Your wallet balance is too low`, beside the button, with the price, your
balance and the amount that is missing.

An app with a monthly price is charged when it is first deployed, not when it is
created, or when a paid month has ended and a new one starts. If the balance does
not cover that charge the deploy is refused and no money moves. **What to do:**
press `Top up` in the notice, then press the button again once the top-up is
credited. The page
[when an action is refused for credit](/billing/credit-and-charges#when-an-action-is-refused-for-credit)
has the detail.

If you still read `Failed to trigger deployment`, the cause is something else,
not your wallet. Inside a paid month, redeploying takes no further payment, so a
redeploy can never fail for lack of credit.

A push to your repository follows the same rule. See
[a push that was not deployed for lack of credit](#a-push-that-was-not-deployed-for-lack-of-credit).

### A push that was not deployed for lack of credit

A push to your connected repository can arrive when your wallet does not cover
the app's monthly price. PivoCloud then deploys nothing. The deployment history
shows one failed row for that push, and the console explains it with these two
sentences:

```text
This push was not deployed because your credit did not cover this app's monthly price.
```

```text
Add credit, then push again or deploy from the console.
```

What this means for you:

- Your previous version keeps running. Nothing about the app changed.
- Nothing was charged for this push.
- Your billing history shows an insufficient balance row for the app.
- PivoCloud does not send you an email about it.

**What to do:** open your wallet and add credit. Then push again, or press
`Redeploy` in the console.

## What happens to your credit when a deploy fails

Only the deploy that made a charge is refunded. It is refunded automatically and
in full, you do not have to ask, and it happens only when no earlier version of
your app is still running. In practice that is a first deploy that fails.

A failed update keeps the month you already paid for. Two cases follow from that:

- **A deploy that paid for a new month fails while an earlier version keeps
  running.** You keep the month, because that earlier version is what you are
  being served.
- **A deploy fails inside a month you already paid.** Nothing is charged and
  nothing is refunded.

Your app shows `Running` when the previous version still serves, because the
update stopped before replacing it. It shows `Failed` when the previous version
was already stopped. Either way the month is kept. You can fix the problem and
deploy again, or roll back, at no extra charge until the month ends.

When a first deploy is refunded, **the next deploy that succeeds charges
again.** The refund cancels the month you paid for, so the paid month starts when
your app actually runs rather than when you first tried. A wallet showing a
debit, then a credit, then a second debit for the same app is one month paid for,
not two.

**A deploy that a platform update interrupts twice follows the same rule.** If
your previous version is still running when the deploy stops, your app keeps
running, and nothing is refunded and nothing is charged again. If nothing was
running, the deploy counts as a failed deploy and the refund described above
applies. The single automatic restart of an interrupted deploy never charges you
a second time.
