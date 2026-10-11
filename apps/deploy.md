---
title: How do I deploy my first app?
description: "Fill every field on the PivoCloud create-app form without guessing: name, subdomain, plan, repository, branch and build settings. Plus the port rule the platform really enforces, and the two messages a failed first deploy prints."
last_verified: 2026-10-07
---

## Deploy your first app

### What the platform enforces

PivoCloud builds the Dockerfile in your repository, reads the first `EXPOSE`
line in it, and probes your app on that port. There is no build detection and no
framework guessing: the runtime is whatever your base image pins.

Three facts follow from that, and between them they account for almost every
first deploy that fails.

**No `PORT` variable is set for you.** PivoCloud does not inject one. Your
process has to be listening on the port its own `EXPOSE` line declares. A
Dockerfile with no `EXPOSE` line is not refused: PivoCloud probes port `8000`
instead, and unless your app is listening there the deploy fails with the second
message at the bottom of this page. Reading `PORT` and falling back to that
number is the portable way to write it, because other hosts do set the variable:

```js
// Dockerfile says: EXPOSE 8080
const port = process.env.PORT || 8080;
app.listen(port, "0.0.0.0");
```

**Bind `0.0.0.0`, not `localhost`.** A server bound to `127.0.0.1` inside a
container is reachable by nothing outside it.

**An app serves exactly one HTTP port.** A backend plus a frontend is either one
image serving both, or two apps built from one repository, each with its own
build settings.

The other three rules, the Dockerfile itself, your migrations and the ephemeral
filesystem, are on [what your repository needs](/apps/deployment-contract) with
worked examples. Read that page once before you create anything.

### The list the form shows you

The create form displays a short checklist headed `Platform Requirements` above
the fields. Two of its three items are out of date, and where it and this page
say different things, this page is correct.

No environment variable carries the port for you, and the port your app must
listen on is the one the first `EXPOSE` line in your Dockerfile declares.

Your Dockerfile does not have to sit at the repository root either. The
`Root directory` and `Dockerfile path` fields, both documented further down this
page, build from wherever it actually lives.

### Create the app, field by field

On `My Apps` with nothing deployed yet, the page shows an empty state and one
button, `Create New App`. It opens the form. The fields below are in the order
the form renders them.

**`App Name`.** The name you and your team see in the console. The helper reads
`Use lowercase letters, numbers, hyphens, and underscores only` and the field
suggests `my-awesome-app`. Pick something you would recognise in a list a year
from now.

**`Subdomain`.** The hostname your app answers on. It has its own section below,
because it is the one field with a decision in it.

**`Plan`.** How much machine your app gets, and what it costs per month. `Lite`
is the entry plan. The list shows each plan next to its monthly price, and once
you pick a paid plan the form tells you what creating the app will charge and
what your balance becomes. If you claimed a starting credit, it is spent like any
other credit on your balance. What each plan costs, and so how far your balance
goes, is on [what does it cost, and when am I charged](/billing/credit-and-charges).

**The repository.** If GitHub is not connected yet, the form shows a
`Connect GitHub` button and the line `You'll be taken to GitHub to authorize the
PivoCloud App. You'll return here afterward.` Once connected, the picker is
labelled `Pick a repository`, with a `Search repositories…` field inside it.
Which repositories appear there is decided entirely by your App installation:
see [connecting GitHub](/apps/connect-github).

**`Auto-deploy on push`.** A toggle, offered on the GitHub App path. Its helper
reads `Deploys automatically when a push targets the deploy branch.` Leave it on
unless you want every deploy to be a deliberate act.

**`Advanced`.** A collapsed section, covered below. It folds itself away as soon
as the form can read your repository through the App, so on the recommended path
you will not see it at all.

**`Environment variables (optional)`.** Its own collapsed section, offered only
when you are creating an app. Anything your app needs at runtime, API keys,
database URLs, feature switches, can go in here now or be set afterwards. See
[environment variables](/apps/environment-variables).

**`Deploy branch`.** Which branch is built. It suggests `main`, and its helper
reads `Branch to deploy. Defaults to your repository's default branch.`

**`Root directory`.** The helper: `The folder Docker builds from. Leave it blank to build from the repository root.`

**`Dockerfile path`.** The helper: `The path to the Dockerfile, relative to the root directory above. Leave it blank to use the default filename.`

Submit with `Create App`.

### When your Dockerfile is not at the repository root

Two fields cover this, and getting the second one wrong is the most common
mistake on the whole form.

- `Root directory` is the build context: the only part of your repository Docker
  can see. Nothing above it exists as far as the build is concerned. Leave it
  blank and the context is the repository root.
- `Dockerfile path` is the file to build, **resolved relative to the root
  directory above**, not to the repository root. Leave it blank and the default
  filename is used.

So an API living in `backend/` with a `Dockerfile` beside it wants
`Root directory` set to `backend` and `Dockerfile path` set to `Dockerfile`. Not
`backend/Dockerfile`, which would resolve to `backend/backend/Dockerfile`. The
form prints the resolved pair back to you as you type, in this shape:

```
Builds backend/Dockerfile with build context backend.
```

Read that line before you submit. It is the cheapest way to catch the doubled
prefix.

Two apps can be built from one repository this way, each with its own root
directory. The
[monorepo example](https://github.com/PivoCloud/example-monorepo-two-apps) is
that shape end to end.

### Choosing the subdomain

The field starts filled in for you, derived from the app name as you type it.
The moment you edit it yourself, that link is cut permanently: the field stops
following the name, even if you clear what you typed.

The rules are: between 3 and 63 characters, lowercase letters, numbers and
hyphens. It cannot start or end with a hyphen, and it cannot start with `app-`,
which is reserved for the addresses PivoCloud generates. A further set of names
is reserved as well, and when one of them applies the verdict beside the field
names the rule you broke. The console appends your account's app domain after
it, and shows you the full address you are about to get.

The two rules a first name most often trips print their own message:
`Can't start or end with a hyphen.` and
`Subdomains can't start with "app-". That prefix is used for automatic URLs.`

Where the name is optional, leaving it blank is a perfectly good choice. Do that
and PivoCloud builds a hostname from the app's own id, and shows it to you before
you submit in the shape `app-4f3c1a2b…`. You can pick a real name later. Where
the name is required, which is the case for an address under `pivocloud.app`
described next, the field is marked as required and the form will not submit
without it.

As you type, a small verdict appears beside the field. It reads `Checking…`
while the console asks, then one of `Available`, `Taken`, `Reserved`, `Invalid`
or `Check unavailable`.

Treat that verdict as a hint rather than a reservation. It tells you what was
true a second ago, not what will be true when you submit: a name can show
`Available` and still be refused if someone else creates it first. Nothing holds
a subdomain for you until the app exists.

Until the first deploy you can still change it on an address that can be
changed. The console says so where the address is shown: `This URL starts working
the first time you deploy. You can change it until then without using up a
certificate.`

### Your app's address

The address shown on your app's page is the one your app answers on, and it never
changes by itself. Apps created before PivoCloud began giving out addresses under
`pivocloud.app` keep their `apps.pivocloud.com` address, and it keeps working.
As this rolls out, a new app gets an address under `pivocloud.app` instead. The
form and the app page always show you which one you have, so read the address
there rather than assuming it.

An address under `pivocloud.app` follows four rules:

- **The name is required.** There is no generated fallback. The field starts
  filled from your app name, and you can edit it. If the name is taken, the
  verdict beside the field says so and you choose another.
- **Some names are reserved** for PivoCloud's own use. When you type one, the
  verdict beside the field says it is on the reserved list and asks you to try
  another name.
- **A name you stop using stays yours.** After you delete an app, nobody else on
  PivoCloud can ever take its name, so old links and webhooks cannot be picked up
  by a stranger. You can use the name again for a new app on your own account.
- **The address cannot be renamed.** The app page shows it without a change
  control. If you need a different name, create a new app with it.

PivoCloud makes your address reachable before it shows it to you, so the link on
your app's page works as soon as you see it. If PivoCloud cannot set the address
up when you press `Create App`, no app is created and the form shows this
sentence, with everything you typed still in place:

`We couldn't set up your address. Try again in a few minutes.`

Press `Create App` again after a few minutes. If it keeps failing, contact
support and quote that sentence.

### Deploying without the GitHub App

The `Advanced` section is the path for a repository the App connection cannot
reach: a repository you do not want to grant the App, or a one-off you would
rather not install anything for. It holds three controls: a repository URL, a
toggle marked `Private repository`, and, once that toggle is on, a field for a
GitHub token with read access to the repository contents.

The section hides itself as soon as the form successfully reads your repository
through the App, which is why most people never open it. Prefer the App
connection where you can: it is what makes deploying on every push possible, and
it means no token of yours has to live here.

When the repository was picked through the App, the form does not ask for a
token at all. Under `Private repository` it shows this line instead:

`Access to this repository goes through your GitHub connection. No token is needed.`

An app that still holds a token saved from before it moved onto the App
connection shows one more line next to a `Remove token` button:

`A token saved earlier is still stored but is not used for this app.`

### Migrations

Nothing runs your migrations. There is no release phase and no automatic
migration step, so a schema change is yours to trigger explicitly. The
entrypoint pattern that makes it opt-in, so a container restart cannot surprise
you, is on [what your repository needs](/apps/deployment-contract).

### Watch the build, then reach your app

Creating the app takes you to its page, which is organised as five tabs:
`Overview`, `Deployments`, `Environment`, `Domains` and `Billing`.

- `Deployments` carries the build log. Watch it here on the first deploy: this
  is where a failing build tells you why. If your build prints the value of one
  of your environment variables, the log shows `•••REDACTED•••` in its place.
  This cannot be undone for a deploy already stored. If PivoCloud could not load
  your app's values, the log shows `Build output is hidden because PivoCloud could
  not load this app's values to blank them. Deploy again to see it.` Deploy again.
- `Domains` carries the address your app answers on, with its certificate state,
  and, for an address that can be changed, is where you change the subdomain
  before the first deploy.
- `Environment` is where you add or change environment variables. Before the
  first deploy, saving only stores them: there is no container yet to
  replace. Saving a change afterwards replaces the running container rather
  than rebuilding the image, so it is fast. See
  [environment variables](/apps/environment-variables).
- `Overview` carries the app's status and which repository it came from.
- `Billing` carries what this app costs and what it has cost.

The first deploy starts when you press `Deploy` on the app page. It does not
begin on its own, so add your environment variables first if your app needs
them at startup. After that, the button on the app page reads `Redeploy` and
rebuilds from your deploy branch on demand.

### Reading the deployment history

The history on the `Deployments` tab lists your recent deployments, newest first.
It shows the 10 most recent. Its columns are `Status`, `Timestamp`, `Type`,
`Commit` and `Duration`.

**Type.** Each row says what happened to your app:

- `Deploy` built your repository and started a new version.
- `Env vars updated` replaced the running container with one that has your new
  variables. Nothing was rebuilt.
- `Restarted` started your app again from the version it last built, for
  example after a stop or a plan change. If that version is no longer on the
  server, PivoCloud rebuilds it from your repository instead. The row still says
  `Restarted`, and it shows the commit that rebuild used.
- Any other row reads `Other`.
- `Stopped` stopped your app.
- `Domain configured` and `Subdomain configured` changed the address your app
  answers on.

**Commit.** For a deploy, the row shows the short commit, its message and its
branch. Clicking the short commit opens it on GitHub. The link is built from the
repository your app is connected to now, so it may not open a commit that
belongs to a repository you connected earlier. `Env vars updated` and
`Restarted` rows show the commit that was already running, not a new one. The
one exception is a `Restarted` row that had to rebuild, as described above.

Two texts stand in when there is no commit to show. `Commit not recorded` means
PivoCloud has no commit for that row. It appears on a deploy that has not cloned
your repository yet, on a deploy that failed before the clone finished (a wrong
repository URL, for example), and on rows from before PivoCloud recorded commits.
`No code change` marks a row that runs no code, such as a stop or a domain
change.

**Live and Latest.** `Live` marks the deployment your app serves right now.
`Latest` marks the newest deploy, env var change or restart, but only when it is
not the live one. `Latest` can be a deploy that is still in progress. A domain
change or a stop is never marked `Latest`.

The two differ when a newer deploy failed or is still building. While a build is
broken, your previous version keeps serving, so it stays `Live` and the failed
attempt shows as `Latest`. A stopped app has no `Live` row, and it gets one again
when you start it.

A deploy that builds but then fails its health check leaves no `Live` row. The
previous version was already replaced when the new one started, so no row can
claim to be what runs now. The same is true while a deploy is starting. Fix the
problem and deploy again.

Because the list shows only the 10 most recent deployments, a live deployment
older than those has no `Live` row in the list. The card above the history still
names the commit your app runs now.

### If the first deploy fails

Two messages account for most first failures, and both are worth reading
literally.

The first says the build found nothing to build at the path your two settings
resolved to, and then lists the Dockerfiles it did find in your repository. One
of the two settings is off. Compare the path in the message against the list
underneath it, and check `Root directory` first.

The second message reports that your container started but nothing answered on
the port PivoCloud probed.

Check your `EXPOSE` line against the port in your own startup log, then read the
container logs. If your Dockerfile has no `EXPOSE` line at all, PivoCloud probed
port `8000`, so there is nothing for you to compare and the fix is to declare
the line.

Both messages, and every other one PivoCloud prints when a deploy does not work,
are on [why did my deploy fail](/apps/troubleshooting) with what each one really
means. The contract your repository has to satisfy, including the whole `EXPOSE`
rule, is on [what your repository needs](/apps/deployment-contract).
