---
title: How do I deploy my first app?
description: "Fill every field on the PivoCloud create-app form without guessing: name, subdomain, plan, repository, branch and build settings. Plus the port rule the platform really enforces, and the two messages a failed first deploy prints."
last_verified: 2026-09-06
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
is the entry plan at 1,200 DA per app per month, which is exactly what the
starting credit covers for a first month. The list shows each plan next to its
monthly price, and once you pick a paid plan the form tells you what creating
the app will charge and what your balance becomes.

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

    Builds backend/Dockerfile with build context backend.

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

Leaving it blank is a perfectly good choice. Do that and PivoCloud builds a
hostname from the app's own id, and shows it to you before you submit in the
shape `app-4f3c1a2b…`. You can pick a real name later.

As you type, a small verdict appears beside the field. It reads `Checking…`
while the console asks, then one of `Available`, `Taken`, `Reserved`, `Invalid`
or `Check unavailable`.

Treat that verdict as a hint rather than a reservation. It tells you what was
true a second ago, not what will be true when you submit: a name can show
`Available` and still be refused if someone else creates it first. Nothing holds
a subdomain for you until the app exists.

Until the first deploy you can still change it. The console says so where the
address is shown: `This URL starts working the first time you deploy. You can
change it until then without using up a certificate.`

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

### Migrations

Nothing runs your migrations. There is no release phase and no automatic
migration step, so a schema change is yours to trigger explicitly. The
entrypoint pattern that makes it opt-in, so a container restart cannot surprise
you, is on [what your repository needs](/apps/deployment-contract).

### Watch the build, then reach your app

Creating the app takes you to its page, which is organised as five tabs:
`Overview`, `Deployments`, `Environment`, `Domains` and `Billing`.

- `Deployments` carries the build log. Watch it here on the first deploy: this
  is where a failing build tells you why.
- `Domains` carries the address your app answers on, with its certificate state,
  and is where you change the subdomain before the first deploy.
- `Environment` is where you add or change environment variables afterwards.
  Saving a change replaces the running container rather than rebuilding the
  image, so it is fast. See [environment variables](/apps/environment-variables).
- `Overview` carries the app's status and which repository it came from.
- `Billing` carries what this app costs and what it has cost.

The first deploy starts on its own. After that, the button on the app page reads
`Redeploy` and rebuilds from your deploy branch on demand.

### If the first deploy fails

Two messages account for most first failures, and both are worth reading
literally.

*"No Dockerfile at `<path>` (build context: `<dir>`)"*, followed by a list of
the Dockerfiles that were actually found in your repository. One of your two
build settings is off. Compare the path in the message against the list
underneath it, and check `Root directory` first.

*"The container started but your app did not respond on the PORT environment
variable. Please ensure your app listens on the port provided via the PORT
environment variable."*

Read the first sentence as **"we could not reach your app on the port it
declared"**, and ignore the second: no such variable is set, so there is no
value for your app to listen on. It is printed for nearly every boot failure,
not only for port mistakes. Check your `EXPOSE` line against the port in your
own startup log, then read the container logs. If your Dockerfile has no
`EXPOSE` line at all, PivoCloud probed port `8000`, so there is nothing for you
to compare and the fix is to declare the line.

Both are explained at greater length, with the fixes, on
[what your repository needs](/apps/deployment-contract).
