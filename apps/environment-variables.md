---
title: How do I set environment variables for my app?
description: "Paste a .env file into the PivoCloud console, see which five key names are refused and why, what the console keeps from your file and what it drops, what saving actually changes, how to see who changed a variable and undo a change, and how your running app reads the values."
last_verified: 2026-10-30
---

## Environment variables

Everything your app reads at runtime goes here: API keys, database URLs, feature
switches. They live on the app's `Environment` tab, in a panel headed
`Environment variables`.

You can also set them while you create the app, in the collapsed
`Environment variables (optional)` section of the create form. Same variables,
same rules, one less trip.

Every example value on this page is made up. Do not paste a real secret into an
app that prints its own environment to a web page.

### Two ways to edit

The panel has two modes, `Editor` and `.env text`.

`Editor` gives you one row per variable, which is what you want for changing a
single value. `.env text` gives you the whole set as one file, which is what you
want if you already have a `.env` and would rather paste it than retype it.

While the panel loads your variables, `Add env var`, the rows, `.env text` and the
save button stay greyed out. That way nothing you type is overwritten by the load,
and no save goes out against a set the console has not read yet. If loading
fails, the panel says `Environment variables could not be loaded.` with a
`Try again` button, and nothing is editable until a load succeeds. If the app
no longer exists, the panel says `This app was not found.` instead, with no
`Try again` button, because trying again cannot help. If only the
history fails to load, `Recent changes could not be loaded.` shows under
`Recent changes` and editing works as usual.

### Pasting a .env file

Values are hidden until you press `Reveal values to edit`. Until you do, the
text is read-only, because the panel will not put your secrets on screen unless
you ask for it. Once they are showing, the console says so:
`All values are visible. Avoid this while screen sharing.`

The text area is labelled `Environment variables as .env text`, and its
placeholder shows the shape it expects: `KEY=value, one variable per line`.

Paste your file in. The console reads it with its own rules, and it will tell
you what they are: press `How this is read` beside the text area and a panel
headed `How PivoCloud reads this file` opens. Four of those rules are worth
knowing before you paste.

**A comment line is read and then dropped.** A line beginning with `#` is a
comment and is never stored as a variable. A `#` inside a value with no space in
front of it is part of the value, not the start of a comment: `DB_PASS=pa#ss22`
stores `pa#ss22`, where some other tools would cut the value at the `#`.

**A dollar-brace reference is kept as text.** `${VAR}` and `$(cmd)` are stored
literally and never expanded. Your container receives the characters exactly as
you typed them, so a password that happens to look like a shell expression
arrives intact.

**A multi-line block survives the paste.** A `-----BEGIN` block is read as one
multi-line value, up to its matching `-----END` line, so a PEM-formatted key can
go in as it stands rather than being folded onto one line.

**What comes back is the same variables, not the same file.** Comments, blank
lines and quote style are not stored. Save, reopen, and you see every variable
you pasted, formatted by PivoCloud rather than in your original layout. The
values round-trip; the layout does not.

### Merging, or replacing everything

Under the text area sits a switch labelled `Replace all variables`. It decides
what happens to variables that are set on the app but absent from the text you
pasted.

Leave it off and the console says
`Merging. Variables not in your text are kept.`
Your text adds and updates, and nothing is deleted. This is the safe default and
the one you want when you are pasting a partial file.

Turn it on and it says
`Replacing. Variables not in your text will be deleted. You will confirm the list before anything is written.`
The save button changes to `Review and replace…`.

Nothing is deleted before you have seen the list. Pressing `Review and replace…`
opens a dialog titled `Replace all variables?` that names every variable that
would be permanently deleted and asks you to type a word to confirm, prompting
`Type REPLACE to confirm`. After you confirm, the deleted variables can be undone
from `Recent changes`, as long as the replace is one of the 20 most recent
changes and none of the deleted variables has been changed again since. See "Recent changes and undo" below.

### The five names the platform keeps

Five key names are refused. Submit one and the console shows your key followed
by the sentence `is reserved by the platform.`

The match is exact. Only those five exact names are
refused, so a key that begins with one, contains one, or ends with one is an
ordinary key and goes in like any other:

- `PORT` is refused, while `PORTAL` and `PORT_NUMBER` are accepted.
- `PATH` is refused, while `MY_PATH` and `PATHFINDER` are accepted.
- `HOME` is refused, while `HOMEDIR` and `HOME_PAGE_URL` are accepted.
- `HOSTNAME` is refused, while `HOSTNAME_PREFIX` and `DB_HOSTNAME` are accepted.
- `USER` is refused, while `USER_ID` and `DB_USERNAME` are accepted.

Four of them are set by the container's own operating system, and shadowing one
breaks your app in a way that is hard to read from the outside: an entrypoint
that cannot find its binaries, or a home directory that does not exist.

`PORT` is the fifth, and it is the one that surprises people. **The name is
reserved, and PivoCloud does not set a value for an app built from a
Dockerfile.** PivoCloud reads the first `EXPOSE` line in your Dockerfile and
probes your app on that port, so that is the port your app has to listen on. The
name stays reserved so that saving a variable can never move the port a running
app is reached on. That contract, and the one-line pattern that keeps the same
image portable to other hosts, is on
[what your repository needs](/apps/deployment-contract).

### A key can appear only once

A duplicate key, the same key twice in one save, is refused. In `.env text` mode the console
points at the second one, for example `API_KEY is already set on line 3.` In the
`Editor`, and when the request is checked on PivoCloud's side, the message is
`Each key may appear only once.` Keys are compared exactly, so `API_KEY` and
`api_key` are different names (the second is refused for another reason, see
below). Delete one of the two rows, or merge their values by hand, and save
again.

### A lowercase key is refused for a different reason

Keys are uppercase letters, digits and underscores, and may not start with a
digit. A lowercase key is invalid, and the console shows
`Use uppercase letters, digits, and underscores for the key.`

A lowercase spelling of one of the five reserved names breaks both rules at
once, and the console shows both messages for that single row rather than
picking one. A row keyed `path` gets the invalid-key message and
`path is reserved by the platform.` together. Both belong to that one row, and
their order on screen means nothing.

Uppercasing `path` to `PATH` clears the invalid-key message and leaves the
reserved one, because the name itself is what is refused and no spelling of it
is accepted. Pick a different name, such as `MY_PATH`. A key that is only
lowercase, such as `database_url`, gets the invalid-key message on its own, and
uppercasing it to `DATABASE_URL` clears it and the row goes in.

### The size limit on a value

Each value is capped at 64 KiB. Above that the console refuses the row with
`This value exceeds the 64 KiB limit.`

A certificate or a private key fits comfortably. A data file you meant to ship
with the app does not, and belongs in object storage instead.

### What saving actually does

**Before your app's first deploy there is no container to replace, so saving
only stores the variables.** The console shows
`Saved. They will be used at your first deploy.` and the first deploy starts
with them already in place. `Apply now` after attaching or detaching a
database behaves the same way before the first deploy.

Once your app has deployed, saving replaces the running container. It does
not build a new image, and knowing that changes what you wait for.

PivoCloud starts a second container from the existing image, hands it your new
variables, waits for it to answer a health check, moves traffic across, and only
then stops the old one. If the new container fails its check, the old one keeps
serving and your app never goes down. The swap takes seconds rather than the
minutes an image build takes, so if you were watching for a build log, there
will not be one.

**A save that changes nothing does nothing.** If your text matches what is
already stored, the button reads `No changes to save` and no container is
touched. Reopening the panel and saving again is safe.

Two messages can come back instead of a save:

- `A deployment is in progress. Try again in a moment.` Another change to this
  app is still being applied. Wait a few seconds and save again.
- `Environment variables changed. Refresh and re-apply your changes.` Someone
  else, or another tab of your own, saved while you were editing, so what is on
  your screen is out of date. Nothing of yours was written. Reload the page,
  look at the current set, and re-apply your change on top of it.

### Recent changes and undo

Below the editor, `Recent changes` lists what happened to the app's variables. There is
one entry for each save you make, each change an agent makes, and each undo. An entry
shows the names it touched, grouped as `Changed`, `Added` and `Removed`, the time, and who
made it. A change you made reads `by you`. A change an AI agent made shows the agent's
name and the connection it used. If you cut that connection off since, the entry says it
was revoked. An entry never shows a value.

`Undo` on an entry puts back only the variables that one change touched, as they were
just before it. A click opens a preview titled `Undo this change?`. For each variable it
says what will happen: it gets its previous value back, it is removed because that change
added it, or it comes back because that change removed it. If a variable was changed
again after that entry, by you or by an agent, it is left as it is and the preview says so. Undo that later
change first. When every variable of an entry was changed again, the preview says the
change cannot be undone now, and `Undo change` stays off.

The preview keeps the focus on `Keep this change`, so pressing Enter by mistake undoes
nothing. Confirm with `Undo change`.

- **On a running app, one click applies it.** The app switches to the restored values the
  same safe way as a save, and keeps serving while it does.
- **On an app that is stopped, suspended or expired, an undo saves the values and starts
  nothing.** The panel says
  `Saved. Your app is not running, so they will be used when it next starts or is deployed.`
  Before its first deploy, it says `Saved. They will be used at your first deploy.`
- **An undo is a change too.** It appears as a new entry, `Undone by you`, and it can be
  undone in turn.
- **Only the 20 most recent changes can be undone, and not a variable that was changed
  again later.** Older entries read `Too old to undo.`
- **Undo is off while the editor has unsaved edits**, and while a change is being
  applied. Save or discard your edits first.
- **Only you can undo.** An agent has no way to undo a change, its own or yours.

If an undo fails, or the page cannot confirm it, see
[Why did my deploy fail?](/apps/troubleshooting#app_action_failed). To see what an
agent with `Operate` can change, see [What can an agent with Operate do?](/agents/operate).

### How your running app reads them

They arrive as an ordinary process environment. There is no client to install,
no SDK, and no file to read.

```js
// Both of these are set on the Environment tab.
// The fallback is what you get when you run the same image locally.
const databaseUrl = process.env.DATABASE_URL || "postgres://localhost:5432/app_dev";
const signups = process.env.FEATURE_SIGNUPS || "off";
```

The same shape works in any language, because this is the language's own
environment lookup and nothing of ours. Read the variable, fall back to
something harmless, and the one image runs on your laptop and on PivoCloud
without a branch.
