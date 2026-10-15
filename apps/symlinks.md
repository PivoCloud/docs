---
title: Can my repository contain symlinks?
description: "Yes. PivoCloud builds your repository the way docker build does on your own computer: symlinks stay links, special files are skipped, .dockerignore is applied, and files in the image belong to root unless you say otherwise."
last_verified: 2026-10-15
---

## Can my repository contain symlinks?

Yes. PivoCloud packages your repository for the build the same way `docker build`
does on your own computer. If a project builds on your laptop, the links in it
build here too.

The rest of this page says exactly what happens to links, to special files, to
your `.dockerignore`, to the `.git` folder and to file owners. If a build fails
for another reason, read [Why did my deploy fail?](/apps/troubleshooting).

### Links stay links

A link to a file and a link to a folder are both kept as links. They arrive in
the image with the same target you committed. PivoCloud does not open a link and
does not copy what it points to into the build.

Your Dockerfile can then use them like any other path, for example with
`COPY . /app`.

### A link that points outside your repository

A link can point outside your repository. It can use an absolute path, or a path
that climbs above the repository (or above your app's Root directory). It is
still kept as a link, exactly as you committed it.

Inside the image it points at whatever exists at that path inside the image,
which is usually nothing. It never points at anything on PivoCloud's side, and
nothing outside your repository is ever sent to the build. If your app needs the
file, put the file in the repository.

### Files that are not regular files

Named pipes, sockets and device files are skipped. They are never opened and
never sent to the build. A normal Git checkout does not contain them, so you will
rarely see this. When it does happen, the build log tells you how many files were
left out.

### `.dockerignore` is applied

PivoCloud reads your `.dockerignore` and applies it the way `docker build` does.
That includes `!` lines that bring a path back, such as keeping one folder inside
an ignored folder.

```
node_modules
!node_modules/keep
secret.txt
```

A file that your `.dockerignore` excludes is not in the build. Two details to
know:

- If a file named after your Dockerfile exists beside it, with `.dockerignore` at
  the end (for example `Dockerfile.dockerignore`), that file is used instead of
  the one at the root.
- The `.dockerignore` file and your Dockerfile are never excluded, because the
  build needs both. This is the same as `docker build`.

The `.dockerignore` file must be a regular file. If it is a link, a folder or
anything else, PivoCloud does not read it, and your build log says so with one
line. Everything the file lists is then sent to the build, so replace the link
with a regular file.

### The `.git` folder

The `.git` folder is sent to the build unless your `.dockerignore` excludes it.
Some builds read it, for example to stamp a version number, so PivoCloud does not
remove it for you.

If your build does not need it, add `.git` to your `.dockerignore`. The upload
gets smaller and faster. On the repositories we measured, `.git` was about a
quarter to a third of the upload. Do the same for large folders such as
`node_modules`.

### Who owns the files in your image

Files copied into the image belong to root, unless your Dockerfile says
otherwise. This is the same as `docker build` on your computer.

If your app runs as a user other than root and writes into a folder you copied,
that user needs to own it. Use `COPY --chown`:

```
COPY --chown=appuser:appgroup . /app
```

Builds that ran before this change could show the files owned by a different
numeric user. If your app only reads its files, you do not need to change
anything.
