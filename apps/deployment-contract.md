---
title: What your repository needs
description: "The six rules a repository must meet for PivoCloud to build and run it: a Dockerfile, the EXPOSE port contract, binding 0.0.0.0, one HTTP port per app, explicit migrations, and an ephemeral filesystem."
last_verified: 2026-09-06
---

## The deployment contract


Six rules. Meet them and your app deploys. Most deploy failures are one of the
first two.

### 1. A Dockerfile

PivoCloud builds from your Dockerfile. There is no build auto-detection, no
buildpack, and no framework guessing.

This is deliberate, and it is the answer to "which Node/Python/Go versions do
you support": **whichever your base image pins.** The runtime is yours to
choose, not ours to bless, so nothing breaks under you when we upgrade.

By default it builds a file named `Dockerfile` at the root of the repository.
Two optional per-app settings move that, on the creation form and on the app's
settings page afterwards:

- **Root directory** is the build context, the directory Docker can see.
  Default: the repository root. Nothing above it exists as far as the build is
  concerned, so `COPY ../shared` cannot work.
- **Dockerfile path** is the file to build, **relative to the root directory**,
  not to the repository root. Default: `Dockerfile`.

Leave both blank and nothing changes. Set them and a repository with no
Dockerfile at its root deploys fine, and two apps can build two different
Dockerfiles out of one repository. See
[example-monorepo-two-apps](https://github.com/PivoCloud/example-monorepo-two-apps).

When the resolved path is wrong, the deploy fails with *"No Dockerfile at
`<path>` (build context: `<dir>`)"*, and the message then lists the Dockerfiles
it did find in your repository. Read that list first: it usually shows you
exactly which of the two settings is off.

### 2. Listen on the port you declare with `EXPOSE`

**PivoCloud does not set a `PORT` environment variable.** It reads the first
`EXPOSE` line in the Dockerfile it builds, and probes your app on that port, so
that is the port your process has to be listening on.

Write it so the same image is correct everywhere. Read `PORT` if something set
it, because Render, Railway and Heroku all do, and fall back to the value you
declared with `EXPOSE`:

```js
// Dockerfile says: EXPOSE 8080
const port = process.env.PORT || 8080;
app.listen(port, "0.0.0.0");
```

Do not hardcode a value that differs from `EXPOSE`, and do not set `ENV PORT`
in the Dockerfile: a variable baked into the image is one more place for the
two numbers to drift apart.

Three consequences are worth knowing before your first deploy:

- **A Dockerfile with no `EXPOSE` line is not refused.** The build proceeds and
  PivoCloud probes port `8000` instead. If your app is listening on anything
  else, the deploy fails with the message below, and there is no `EXPOSE` line
  for you to check against it. Declare one.
- **It is the first `EXPOSE` in the file, not the one your final stage
  declares.** In a multi-stage Dockerfile, keep `EXPOSE` out of your builder
  stages, or put the runtime one first.
- **Write a literal port number.** `EXPOSE ${PORT}` is not read at all, and
  falls back to the same port `8000` a missing line falls back to. A line
  naming two ports takes the first of them.

When this is wrong, the deploy fails with *"The container started but your app
did not respond on the PORT environment variable. Please ensure your app listens
on the port provided via the PORT environment variable."*

Read the first sentence as **"we could not reach your app on the port it
declared"**, and ignore the second: no such variable is set, so there is no
value for your app to listen on. The message is emitted for almost every boot
failure, not only for port mistakes. If you see it, check `EXPOSE` against the
port in your startup log first, then read the container logs. If your Dockerfile
has no `EXPOSE` line at all, PivoCloud probed port `8000`, so there is nothing
for you to compare and the fix is to declare the line.

### 3. Bind `0.0.0.0`, not `localhost`

A server bound to `127.0.0.1` inside a container is reachable by nothing
outside it.

### 4. One HTTP port per app

An app exposes exactly one HTTP port. If your project is a backend plus a
frontend, you have two choices:

- **Serve the built frontend from the backend.** One repo, one Dockerfile, one
  container, one billed service. See
  [example-node-express-vite](https://github.com/PivoCloud/example-node-express-vite).
- **Deploy them as two apps, out of one repository.** Two Dockerfiles, two
  billed services, one repo: give each app its own root directory and it builds
  its own Dockerfile. Sometimes the right call, especially mid-migration when
  you would rather not change application code at the same time as changing
  host. See
  [example-monorepo-two-apps](https://github.com/PivoCloud/example-monorepo-two-apps).

A worker with no HTTP surface does not fit the app model. Neither do services
that must scale independently.

### 5. Nothing runs your migrations

No release phase, no automatic `migrate` step. If your schema needs migrating,
do it explicitly, and make it opt-in so a container restart cannot surprise
you:

```sh
if [ "$RUN_MIGRATIONS" = "true" ]; then
  npx prisma migrate deploy
fi
exec "$@"
```

[example-node-express-vite](https://github.com/PivoCloud/example-node-express-vite)
and the API in
[example-monorepo-two-apps](https://github.com/PivoCloud/example-monorepo-two-apps)
ship this pattern in their entrypoint.

### 6. The filesystem is ephemeral

Containers are replaced on every deploy, and on an environment-variable change.
Anything written to local disk is gone. There is no persistent disk product.

Uploads belong in object storage (Cloudinary, S3-compatible, anything with an
API). Sessions and caches belong in your database or a managed store, not on
disk.

## Working examples

Three repositories you can deploy on PivoCloud as they are, each one showing a
different shape of the contract above.

- [example-node-express-vite](https://github.com/PivoCloud/example-node-express-vite): Express API serving a built Vite frontend. **One service, one bill.**
- [example-vite-static-nginx](https://github.com/PivoCloud/example-vite-static-nginx): A built Vite frontend served by nginx, as its own service.
- [example-monorepo-two-apps](https://github.com/PivoCloud/example-monorepo-two-apps): An API and a frontend in one repository, deployed as two apps. **Two Dockerfiles, no Dockerfile at the root.**
