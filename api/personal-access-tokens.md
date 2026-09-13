---
title: How do I call the API with a token?
description: "How to create a personal access token in the PivoCloud console, how to send it with a request that starts a database export, what the reply carries, and what to do when the API answers that you should wait rather than starting one."
last_verified: 2026-09-13
---

## Calling the PivoCloud API with a personal access token

The API lets a script do one thing today: start an export of one of your own
databases, and download the result. This page covers that in three parts, in
the order you meet them. Creating a token in the console. Sending a request
with it and reading what comes back. And what to do when the API answers that
you should wait rather than starting an export.

### Create the token

A personal access token is created in the console, and it is the only
credential the API accepts. Every token is fixed to one thing, pulling backups
of your own databases, so there is no scope to choose and no permission to set.

1. Open your profile in the console and find the `API Tokens` section.
2. Press `Create token`. A dialog opens, titled `Create API token`.
3. Type a name into `Token name`. The name is the only thing the dialog asks
   for, and it is there so that you can recognise this token later in the list.
4. Press `Create token` in the dialog.

The full secret is then shown to you once, in a panel that warns you it will
not be shown again. That warning is literal. The list below the panel only ever
shows a masked prefix of each token, and nothing anywhere re-fetches the
secret, so the moment the panel is on your screen is the only moment you can
copy it. Put it wherever your script reads it from before you dismiss the
panel. If you lose it, revoke the token and create a new one.

The same section lists the tokens you already have, with the date each was
created and when each was last used, and a `Revoke` button on every row.
Revoking takes effect at once, so anything still calling the API with that
token starts failing straight away.

### Call the API

One request starts an export. It is a `POST` to the export path for the
database you want, and it carries your token as a bearer credential in the
request's authorization header:

```bash
curl -X POST "https://api.pivocloud.com/api/v1/dbs/00000000-0000-0000-0000-0000000000db/export" \
  -H "Authorization: Bearer pvc_REDACTED000000000000000000000000000000000000"
```

Two values in that line are placeholders and the rest is exactly what a working
call looks like. Put the identifier of the database you want to export in place
of the one in the path, and your own secret in place of the token.

The reply is `200`, and its body carries two fields:

```json
{
  "expires_at": "2027-01-15T08:00:00Z",
  "url": "https://api.pivocloud.com/api/v1/dbs/00000000-0000-0000-0000-0000000000db/exports/00000000-0000-0000-0000-00000000e401/download?exp=1800000000&sig=REDACTED00000000000000000000000000000000000000000000000000000000"
}
```

`url` is the link your export will be served from. `expires_at` is the moment
that link stops working, so a script that stores the link is better off storing
that with it. Both are absolute values the server has already assembled, so there
is nothing on your side to build and nothing to guess at.

The expiry in that example is a placeholder like the other values in it, and it
is not a typical one. A real link is short-lived, minutes rather than days, so
read the moment out of `expires_at` rather than budgeting from what is printed
above. A link asked for after that moment answers `401` instead of serving the
archive, which is the link having expired rather than anything wrong with your
request. Mint the link close to when you will use it, and start a new export if
the one you have has lapsed.

The link carries its own authorisation. The signature inside it is what grants
access, so downloading needs no token and no header at all: a plain `GET` on
that address is the whole download. Treat the link exactly as you treat the
token, because anyone holding it can fetch that dump until it expires. It is
not a public address that merely happens to be long.

The export itself starts after this request returns, so the link is not ready
at the moment you receive it. It answers `503` while the archive is still being
prepared, then serves the archive once it is finished. A script can therefore
ask for an export and poll that one link until it succeeds, without asking for
a status anywhere else.

Poll on a wait and stop on anything else. `503` is a wait, and so is `429`;
both carry the interval to sleep for in their `Retry-After` header. Every other
reply is the end of that loop rather than a step in it. An export that failed
is the one to plan for: the link answers `404` from then on and keeps answering
`404` however long you keep asking, so a loop that reads it as "not ready yet"
runs forever. Treat anything that is not a wait as the export not coming, and
start a new one. The full list of replies each endpoint can send is in the
generated reference rather than on this page, so there is one copy of it and it
comes from the same document the API itself is described by.

### When the API tells you to wait

Two of the export endpoint's answers are a refusal to start a new export right
now rather than a failure, and both arrive as `429`. From the status code alone
they are indistinguishable, and a script that treats them as the same thing
sits out a whole cooldown window for a wait that should have been seconds. Read the code in the body to tell them
apart, and read the wait from the `Retry-After` header of the reply rather than
from a figure written down anywhere. That header carries the number of seconds
to wait, the server sets it per reply, and it is the only value that is true at
the moment you receive it. This page deliberately prints no figure: the
cooldown window is read from configuration when the API starts, so a number
published here would quietly stop being true. A code you do not recognise is
safest treated as a wait as well: ask again after the interval the header
names, rather than stopping the job outright.

`cooldown_active` means this database was exported recently. The wait is the
whole cooldown window and there is nothing to poll, because your request
started no export. This is the one to sleep through.

`export_in_flight` means an export for this database is already running. The
wait is short, seconds rather than a cooldown window, and sleeping through a
full cooldown here is the mistake this section exists to prevent. The better
move is not to ask again at all. The request that started that export already
returned a link, and that link resolves to the archive once the export
finishes, so poll the link you were given.

A third code also arrives as `429` and it is not about your database at all.
`RATE_LIMIT_EXCEEDED` is the limit on how often you may call the API, it
applies to every endpoint here rather than to one database, and the same rule
covers it: read the wait from `Retry-After` and ask again.

### The full reference

Both endpoints are documented in full, with every reply each one can send, and
with a form you can send a real request from:

- [Start a database export](/api-reference/start-a-database-export)
- [Download a finished export](/api-reference/download-a-finished-export)

For backups and exports taken from the console rather than from a script, see
[how do I get my data back](/databases/recovery).
