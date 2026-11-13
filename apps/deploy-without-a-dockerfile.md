---
title: Can I deploy without a Dockerfile?
description: "Deploy a Laravel, Node, Python or Go repository with no Dockerfile: when PivoCloud builds it for you, the port, start commands and language versions, how Laravel gets its key and database, and the limits you should know before you start."
last_verified: 2026-11-08
---

## Deploy without a Dockerfile

You can deploy a Laravel, Node, Python or Go repository that has no Dockerfile.
PivoCloud looks at your code, builds an image for you, and runs it. You do not
write the Dockerfile.

This is the shorter way in. If you need full control of the image, add a
Dockerfile. A Dockerfile always wins, and
[what your repository needs](/apps/deployment-contract) describes that path.

### When PivoCloud builds without a Dockerfile

PivoCloud builds without a Dockerfile when both of these are true:

- There is no file named `Dockerfile` at the root directory of the app.
- You have not set a `Dockerfile path` for the app.

If a Dockerfile is there, PivoCloud builds it, exactly as before. It never tries
the other way instead. If your Dockerfile build fails, the deploy fails.

If you set a `Dockerfile path` and no file exists at that path, the deploy fails
with an error that names the path. PivoCloud does not guess a different build in
that case.

When it builds for you, the first line of the build log says so, and names the
Railpack version it used.

### What it supports

Laravel (PHP), Node, Python and Go. PivoCloud finds out which one you use from
the files in your repository, such as `composer.json`, `package.json`,
`requirements.txt` or `go.mod`.

If PivoCloud cannot tell which language your app uses, or cannot install the
version your app asks for, the deploy fails. If it knows the language but cannot
find how to start your app, the deploy fails too. The build log shows what the
analysis found, just above the error. [Why did my deploy fail](/apps/troubleshooting)
shows the exact message for each case.

The fix is to change what the build log names (a start command, or a version
PivoCloud supports), or to add a Dockerfile.

### The port

PivoCloud sets the `PORT` variable to `8080` for your app. Your app must listen
on that port, on all interfaces, not only on `localhost`. Most
frameworks read `PORT` by default.

To use another port, set `Listen port` in the app's build settings. PivoCloud
then sets `PORT` to that number, and probes your app there. Leave it blank to use
`8080`. `Listen port` applies to apps built without a Dockerfile. An app with a
Dockerfile keeps the port in the first `EXPOSE` line of that Dockerfile.

`PORT` is a reserved name. You cannot add a variable called `PORT` yourself, so a
saved variable can never move the port a running app is reached on. Change the
port with `Listen port`.

### Start command and build command

By default PivoCloud uses the usual start command of your framework. To set your
own, use one of these:

- A `Procfile` with a `web:` line, for example `web: node server.js`.
- A `railpack.json` file with a `deploy.startCommand`. The same file can set the
  build steps.

If you have both, the command in `railpack.json` is the one that runs. The
`Procfile` is used only when `railpack.json` sets no start command.

### Language versions

PivoCloud reads the version your repository asks for:

- Node: `.nvmrc`, or `engines.node` in `package.json`.
- PHP: `composer.json`.
- Python: `.python-version`.
- Go: `go.mod`.

**PHP needs version 8.2 or newer.** PivoCloud cannot install PHP 8.1 or older
without a Dockerfile. If `composer.json` asks for `"php": "^8.1"` or `"^8.0"`,
the deploy fails with "no version available for php 8.1" in the build log. Change
it to `"^8.2"` (or newer) if your app runs on PHP 8.2, or deploy with a Dockerfile
that installs the version you need.

If your app needs one exact version, set it in your repository, in the file listed
above for your language. When you do not set one, PivoCloud uses a default, and
that default can change when the platform updates its builder.

For Node, if `.nvmrc` and `engines.node` name different major versions, the
`.nvmrc` version is installed. Keep them the same, so the answer never depends
on this rule.

### Laravel

A stock Laravel app deploys with no change to your code.

**The key.** Laravel needs an `APP_KEY`. On the first deploy, if you have not
set one, PivoCloud creates one and saves it in the app's variables. The history
of the variable shows that PivoCloud wrote it, and the deploy log says so too.

PivoCloud creates the key once. If you delete `APP_KEY` later, PivoCloud does not
create a second one, because a new key makes data saved with the old key
unreadable (encrypted cookies, for example). The deploy log tells you so. Add
`APP_KEY` back in the app's variables. If you still have the old value, use it.

**No database.** If the app has no PivoCloud database attached, PivoCloud skips
migrations, sets the session driver and the cache to files, and Laravel writes
its logs to stderr, so you read them in the app's logs.

**With a PivoCloud database.** If you attach a PivoCloud PostgreSQL database,
PivoCloud sets `DB_CONNECTION` to `pgsql` and `DB_URL` to the database address
for you. Migrations run each time the app starts. A value you set yourself for
either variable wins, and PivoCloud leaves it alone.

Only PostgreSQL is wired for you. For another database, set the Laravel
variables yourself.

### What counts as a successful start

After the container starts, PivoCloud asks your app for its home page (`/`), with
your app's address as the host name. Any answer below 500 counts as started. A
4xx answer goes live, and the deploy log carries a warning that names the status.
An answer of 500 or more, or no answer, makes the deploy fail.

For Django, add your app's address to `ALLOWED_HOSTS`. If you do not, Django
answers the check with a 400, and the app goes live with a warning but refuses
real visitors for the same reason.

### Limits

Know these before you choose this way:

- **No variables at build time.** Your variables exist when the app runs, not
  while it builds. A value that your build bakes into the code, such as
  `VITE_*` or `NEXT_PUBLIC_*`, is not set during the build, so it is not in the
  built files. If your app needs those, build it with a Dockerfile.
- **No build secrets.** If your `railpack.json` lists build secrets, the deploy
  fails. Remove them from `railpack.json`, or add a Dockerfile. Then deploy again.
- **Files on local disk are lost on redeploy.** Uploaded files, for example in
  Laravel's `storage`, are gone when the app is replaced, which happens on every
  deploy. Use object storage.
- **No background workers and no scheduler.** One process serves your HTTP port.
  A Laravel queue worker or `schedule:run` has nothing to run it.
- **PostgreSQL only for the Laravel database wiring.**
- **A failed Laravel deploy can leave the app down.** If a migration fails while
  the app starts, the app stays down until the next deploy or a rollback. Check
  your migrations before you push.

### If a deploy fails

[Why did my deploy fail](/apps/troubleshooting) quotes every sentence the deploy
log can show, and what each one means. The cases specific to this way of building:

- **PivoCloud cannot build without a Dockerfile right now, or cannot analyse your
  repository.** This is usually on PivoCloud's side, not your code. The build log
  now shows the last lines the analysis wrote before it stopped
  ([Why did my deploy fail](/apps/troubleshooting) shows where). Read them first, because they can point at your
  repository (for example a tool version in its configuration). If they do not,
  redeploy, then write to support if it repeats and include those lines.
- **Your app does not answer on the port PivoCloud probes, but listens on
  another one.** Set `Listen port` to the port your app uses, or make your app
  listen on the `PORT` variable. Then deploy again.
- **Your app answers with an HTTP error.** It is listening, but it returned an
  error. Open the app's logs to find it, fix it and deploy again. For a Laravel app
  this is often a missing `APP_KEY`.
