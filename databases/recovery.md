---
title: How do I get my data back
description: "What PivoCloud keeps of your PostgreSQL database, how to take a backup or an export yourself, how to download and check an export, what a restore really produces and what it costs, how far back each plan lets you go, and what survives losing the machine."
last_verified: 2026-10-17
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

If a platform recovery interrupts a restore before the new database is ready,
the new database shows as failed, with the message explained under
[a restore interrupted by a platform recovery](#database_restore_interrupted).

The charge for that restore is returned to your wallet automatically, and you
do not need to do anything to get it. To get the database, start the restore
again from the same backup.

**A restore cannot be run over the existing database**, and asking for that is
refused. Every restore produces a new database. If what you want is your
original database back under its original name, restore into a new one, move
your application to it, and delete the original once you are satisfied.

Seven answers you can get instead of a restore starting. All seven leave your
source database exactly as it was.

```text
A restore always creates a new database from the backup, and your current database is not changed. Choose a name for the new database that differs from the name of the current one, and do not ask to restore over the existing database.
```

You meet this one when the name you give the new database is the same as the
name of the database you restored from, in the restore dialog or in an API
call, or when an API request asks to restore over the existing database.
Nothing was taken from your wallet. Ask again with a different name for the
new database.

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

**A nightly backup can be skipped to protect storage.** If the database
server is short of storage when your nightly backup is due, that night's
backup is not taken. Your backup history then shows it as failed, with the
message explained under [a skipped backup](#backup_skipped_low_storage).
Your most recent successful backup and every other kept backup stay as they
were; nothing is deleted to make room. The next night's backup runs as usual.
If you see this message two nights in a row, contact support.

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

## When a database, a backup or an export fails

When a database, a backup or an export does not complete, the console shows a
red message. It says what did not complete, whether your data is safe, and what
happens next. The sentences below are the ones the console shows, quoted
exactly, one heading for each, so you can search this page for the sentence you
see.

A failed database is titled `Database failed` on its card and on its page. The
`Contact support` button on it opens an email already addressed to us, whose
subject names the database and carries the reference, for example
`DBaaS database failed: orders (reference s-3f9a1c2e)`.

### The reference

Some messages end with a reference such as `s-3f9a1c2e`. The letter says what
failed: `s-` is a database, `b-` is a backup and `e-` is an export. It is
followed by 8 letters and digits. A reference is shown only when the cause is on
PivoCloud's side or could not be identified. When you can fix the cause
yourself, as with a suspended database below, there is no reference.

If you write to us, send the reference, the name of the database and roughly
when it happened. With those we find the exact failure without asking you
anything else. Write to `contact@pivocloud.com`, or use the `Contact support`
button on a failed database.

### A backup did not complete {#backup_failed}

```text
Your backup did not complete. Your data is safe and your earlier backups are kept.
```

```text
Our team has been notified. The next daily backup runs automatically. If backups are still failing tomorrow, contact PivoCloud support and give them the reference shown with this message.
```

```text
Our team has been notified. Start a new backup, or wait for the next daily backup. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

A backup that fails does not remove or change the backups you already have.
PivoCloud's team is told about it. The next daily backup runs by itself. For a
backup you started yourself, the console also suggests starting a new one. The
first next step above is for a scheduled backup and the second for a backup you
started.

### A backup was skipped to protect storage {#backup_skipped_low_storage}

```text
This backup was skipped to protect storage on the database server. Your most recent backup is kept.
```

```text
Our team has been notified. The next daily backup runs automatically. If backups are still being skipped tomorrow, contact PivoCloud support and give them the reference shown with this message.
```

This backup did not run, because taking it could have filled the storage the
databases share. Nothing is deleted to make room, and your most recent backup is
kept. PivoCloud's team is told about it, and the next daily backup runs by
itself. The backup history shows the skipped backup as failed, with this
message.

### A backup was interrupted by a platform recovery {#backup_interrupted}

```text
This backup was interrupted by a platform recovery and did not finish. Your data is safe and your earlier backups are kept.
```

```text
The next daily backup runs automatically, or you can start a new backup now. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

A recovery of the platform stopped this backup before it finished. Your data is
safe and your earlier backups are kept. The next daily backup runs by itself, or
you can start one now. PivoCloud's team is not told automatically about an
interrupted backup, so if the next one fails too, write to us with the
reference.

### A backup did not complete because the database was suspended {#backup_suspended}

```text
This backup did not complete because your database was suspended for a low wallet balance.
```

```text
Top up your wallet to start your database again. A new backup starts automatically when it is running again.
```

A database is suspended when your wallet balance is too low to keep it
running. A backup that was running when that happened stops. There is no
reference, because you can fix this yourself: top up your wallet and the
database starts again, and a new backup starts by itself once it is running.

### An export did not complete {#export_failed}

```text
Your export did not complete. Your database was not changed.
```

```text
Start a new export. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

An export only reads your database, so your database was not changed. Start a
new export from the `Exports` section. PivoCloud's team is not told
automatically when an export fails, so if it fails again, write to us with the
reference.

### An export was interrupted by a platform recovery {#export_interrupted}

```text
This export was interrupted by a platform recovery and did not finish. Your database was not changed.
```

```text
Start a new export. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

A recovery of the platform stopped this export before it finished. An export
only reads your database, so your database was not changed. Start a new export.
PivoCloud's team is not told automatically about an interrupted export, so if it
fails again, write to us with the reference.

### PivoCloud could not finish setting up your database {#database_setup_failed}

```text
PivoCloud could not finish setting up your database.
```

```text
Our team has been notified. If you need help sooner, contact PivoCloud support and give them the reference shown with this message.
```

The database was ordered but its setup did not finish. PivoCloud's team is
told about it. If you were charged for this database, say so when you write to
us, and give the reference.

### PivoCloud could not finish deleting a database {#database_delete_failed}

```text
PivoCloud could not finish deleting this database. Its data is kept.
```

```text
Our team has been notified. If you need help sooner, contact PivoCloud support and give them the reference shown with this message.
```

The deletion did not finish, and the data of the database is kept. PivoCloud's
team is told about it.

### A database could not be started again {#database_resume_failed}

```text
Your database could not be started again. Your data is safe.
```

```text
Our team has been notified. If you need help sooner, contact PivoCloud support and give them the reference shown with this message.
```

This happens after you top up a suspended database and it does not start
again. Your data is safe. PivoCloud's team is told about it.

### A restore did not complete {#database_restore_failed}

```text
Your restore did not complete. Your original database and its backup were not changed, and the charge for this database was refunded to your wallet.
```

```text
Our team has been notified. You can start the restore again from your backup. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

A restore writes only to the new database, so the database you restored from
and its backup were not changed. The charge for the new database was returned to
your wallet. PivoCloud's team is told about it. You can start the restore again
from the same backup.

### A restore was interrupted by a platform recovery {#database_restore_interrupted}

```text
This database was interrupted by a platform recovery before it was ready, and its charge was refunded to your wallet.
```

```text
Start the restore again from your backup. If it fails again, contact PivoCloud support and give them the reference shown with this message.
```

A recovery of the platform stopped the restore before the new database was
ready, and the charge for it was returned to your wallet automatically. Start
the restore again from the same backup. PivoCloud's team is not told
automatically about this one, so if it fails again, write to us with the
reference.

### A database stopped working for a reason PivoCloud could not identify {#database_failed}

```text
This database stopped working for a reason PivoCloud could not identify.
```

```text
Contact PivoCloud support and give them the reference shown with this message.
```

PivoCloud has no recorded cause for this failure, so this message claims
nothing about your data. Write to us with the reference. The `Try again` button
is offered only for this failure: it creates a new database with the same name
and plan, which is charged like any other database, so use it only if you want
a second one.
