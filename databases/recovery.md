---
title: How do I get my data back
description: "What PivoCloud keeps of your PostgreSQL database, how to take a backup or an export yourself, how to download and check an export, what a restore really produces and what it costs, how far back each plan lets you go, and what survives losing the machine."
last_verified: 2026-09-09
---

## Backups, exports and restores

This page covers PostgreSQL databases.

Everything on this page is on your database's own page in the console and you
can do all of it yourself. Read the restore section before you use it. A
restore does not do what the word suggests, and it takes money from your wallet
at the moment you ask for it rather than when it finishes.

### What you can do yourself

- **Take a backup now.** `Trigger backup`, in the `Backups` section.
- **See the backups you have.** `View history`, with the number of backups you
  have in brackets after it. It opens a sheet listing them under the columns
  `When`, `Status`, `Kind`, `Size`, `Duration` and `Action`.
- **Restore one of them.** `Restore`, on the row of the backup you want.
- **Take an export now.** `Export database`, in the `Exports` section.
- **Download an export.** `Download`, on that export's row once it is ready.

Backups are also taken for you on a schedule. `Trigger backup` is for the
moment you want one before you do something risky, and it adds to the
scheduled ones rather than replacing them.

Two answers you can get instead of a backup starting:

```text
A backup is already running for this database. Please wait for it to complete.
```

One backup runs at a time for a database. Wait for the one in flight to finish,
then ask again.

```text
Database not found
```

The database is not there, or it is not one of yours. Open it from your list of
databases rather than from a saved link, which may point at one you deleted.

### What a restore actually does, and what it costs

**A restore gives you a second database. It does not put your data back into
the database you restored from.**

`Restore` opens a dialog titled `Restore into a new database`, and the dialog
says the same thing in its own words: `This creates a new database from this
backup. The source database stays untouched.` You name the new database in
`Name for the new database`, and `Confirm restore` starts it.

Four things follow from that, and all four are worth reading before you click.

- The new database gets **its own hostname, its own credentials and its own
  certificate**. It is a different database, not a repaired copy of the old one.
- The database you restored from is **untouched**. It keeps running, keeps its
  connection string, and keeps its own backups.
- **You are charged when you ask, not when it finishes.** The monthly price of
  the plan your source database is on is taken from your wallet at the moment
  the restore is accepted. The dialog states the amount on its own line before
  you confirm.
- The new database then bills on its own cycle like any other database, so
  **you are paying for two databases until you delete one of them**.

Nothing moves your application over for you. Take the new connection string
from the console and point your application at it when you are ready, and if
you pinned the old database's certificate, pin the new one as well. The
[connect page](/databases/connect) covers both.

A restore is a job you watch rather than a click that returns. The new database
appears in your list immediately and stays in a restoring state until it is
ready to accept connections.

**A restore cannot be run over the existing database**, and asking for that is
refused. Every restore produces a new database. If what you want is your
original database back under its original name, restore into a new one, move
your application to it, and delete the original once you are satisfied.

Six answers you can get instead of a restore starting. All six leave your
source database exactly as it was.

```text
Insufficient wallet balance.
```

Your wallet does not hold the source database's monthly price. Top it up, then
ask again. The console also disables the confirm button when it can already see
the balance is short, so you may meet this as a button you cannot press rather
than as a sentence.

```text
Backup is not in a restorable state (must be 'success').
```

That backup did not complete, so there is nothing to restore from it. Pick a
row whose `Status` reads as successful, or take a new backup and restore from
that one.

```text
Database or backup not found
```

Either the database or the backup you named is gone. Reopen the history sheet
and pick a row from it rather than reusing an identifier you saved earlier.

```text
Database capacity is temporarily unavailable.
```

There was no free capacity to build the new database into. Nothing was charged.
Wait a minute or two and ask again.

```text
this database was created before the platform started recording which server runs it, so a restore has nowhere to run. Contact support to have it linked, then restore again
```

This one needs us. Email `contact@pivocloud.com` with the database's name, and
the restore will work once it is linked.

```text
the platform could not reserve capacity on the server that runs this database, so a restore would have started somewhere else. Nothing was changed and no credit was taken. Please try again in a few minutes, and contact support if it keeps happening
```

The message says what to do: nothing happened, no credit was taken, try again
shortly. If it keeps happening, email `contact@pivocloud.com`.

### Exports, and downloading one

An export is a compressed plain SQL dump of your database, produced when you
ask for one and downloaded from the console. It is a file you keep. A backup
lives on the platform and is what a restore reads; an export is yours to store
wherever you like and to load into anything that speaks PostgreSQL.

`Export database` starts one. The row underneath moves through four states
while it works: it is queued, then it is running, then it either finishes with
a `Download` button beside it or it does not finish and the row tells you why.
Only one export runs at a time for a database, and there is a wait between one
export and the next.

The file you get is named after the export's own identifier with a `.sql.gz`
suffix, not after your database, so rename it if you are keeping several. The
console shows a SHA-256 checksum next to the export and the download carries
the same value in an `X-Export-Sha256` response header, so you can prove the
file you have is the file the platform made:

```bash
sha256sum <downloaded-file>
```

Compare that against the checksum on the row. The export is not encrypted, so
where you put it is where its security comes from.

Exports do not stay available forever. Each export's own row shows when it
expires, and that is the number to read: it is computed for that export, and a
figure written on this page would not be.

Four answers you can get instead of an export starting or downloading:

```text
An export is already running for this database. Please wait for it to complete.
```

```text
Please wait before requesting another export for this database.
```

Both of those mean wait and ask again, and which of the two you get depends on
timing rather than on anything you did differently. There is no need to work
out which one applies to you.

```text
Database not found
```

The database is not there, or it is not one of yours.

```text
Export not found
```

The export is gone, which is most often because it expired. Ask for a new one.

### How far back you can go

Two different things decide that, and reading them as one number is how people
end up surprised.

**The plan your database is on decides whether you can go back to an arbitrary
moment.**

- `Starter` has no point-in-time window at all. What Starter has is its
  backups, described below.
- `Growth` advertises a window of 7 days.
- `Business` advertises a window of 14 days.

**Backups are kept as a count, never as a stretch of calendar.** PivoCloud
keeps the 30 most recent successful backups, plus the most recent backup in
each of up to 12 further calendar months. Read those as counts of backups,
because that is what they are: 30 backups is not the same promise as 30 days,
and how far back your 30 reach depends on how often they were taken.

This is the figure every plan has, Starter included, and it is what a
self-serve restore restores from.

Separately from both of those, a copy of your database is taken off the
platform every 24 hours. What that copy protects is a different thing, and the
next section is about it.

### If the machine holding your database is lost

The window above protects you against something that went wrong inside a
database that is otherwise healthy: a bad migration, a delete without a where
clause, an application writing nonsense for an hour. That is the common case
and the window is the right answer to it.

It is not what protects you against losing the machine your database runs on.
The copy taken off the platform every 24 hours is what protects you against
that, and in that situation it is the only thing that survives.

So your honest exposure to that second case is those 24 hours rather than the
window your plan advertises. If you want it shorter than that for a particular
moment, take your own export before you do anything risky and keep it
somewhere else. That is the one lever you hold yourself.

### Restoring to a chosen moment

`Growth` and `Business` advertise a window you can be restored to any point
inside, and there is no control in the console for it today. To use it, email
`contact@pivocloud.com` with your database's name and the exact moment you want
to go back to, and we will do the restore for you. Be as precise about the
moment as you can: the point of the window is that it is not limited to the
moments a backup happened to be taken.

The self-serve restore described above is a different thing. It restores a
backup, so it takes you to the moment that backup was taken, and it is
available on every plan.
