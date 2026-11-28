---
title: "Backups: what is kept and how to restore"
description: "Every Pivobase backend is backed up once a day and each backup is kept for 7 days. This page says how backups work for a sleeping backend, what is in a backup file, how to restore one yourself with psql, how to ask us to restore one, and what happens to backups when a backend is deleted."
last_verified: 2026-11-26
---

## How backups work

We back up the database of every backend once a day, and we keep each backup for
7 days. You do not turn anything on and there is nothing to pay for.

The backend page has a `Backups` card. It lists your backups, newest first, with
the date and time of each one and its size. Press `Download` on a row and your
browser saves that backup as one file, named like
`my-backend-backup-2026-11-26-0312.tar.gz`. The time in the name is the time the
backup was taken, in UTC.

A backup appears in the list only after we have checked that it can be restored.
A backup that failed or did not pass the check is not listed.

Opening the page and downloading a backup do not wake a sleeping backend. The
list and the files come from our stored copies, not from your backend.

### A backend that sleeps

A sleeping backend that has not been used since its last backup is left asleep,
and its latest backup is always kept, even when that backup is older than 7 days.
If the backend was used since its last backup, we start it briefly to take the
backup. You are not billed for this, it does not count as use, and the backend
goes back to sleep on schedule.

A new backend shows `No backups yet` for its first day. Its first backup appears
within 24 hours. If the card says your backups are late, contact support and we
will look into it.

## What is in a backup

A backup is one compressed file. It holds your database as plain SQL, in parts
you can read in any text editor:

- `roles.sql`: the database roles.
- `schema.sql`: your tables, functions, policies and the rest of your schema.
- `data.sql`: the rows of your tables.
- `vault.sql`: the rows of your Vault secrets, still encrypted.
- `managed.sql`: row level security, policies and triggers on the `auth` and
  `storage` schemas, and `pg_cron` jobs.
- `README.txt`: the restore steps, the same as on this page.

The first three parts are the ones the Supabase command line writes when you
back up a project, so the restore is the one a Supabase user already knows.
`vault.sql` and `managed.sql` are the two parts we add so that nothing of yours is
left out.

**Vault secrets stay encrypted when you restore them somewhere else.** The key
that opens them is not in the file. If you restore a backup into your own
Postgres, your Vault secrets are restored as encrypted rows and you will need to
set them again. When we restore a backup for you, your Vault secrets come back
readable.

## Restore a backup yourself

You can restore a downloaded backup into any Postgres that is Supabase
compatible, for example a local Supabase or another Supabase project. You need
the `psql` command and the connection string of an empty database.

1. Unpack the file:

   ```
   tar -xzf my-backend-backup-2026-11-26-0312.tar.gz
   ```

2. Create the extensions your database used, before anything else. They are not
   part of `schema.sql`. For example, if your backend used scheduled jobs:

   ```
   psql --dbname "[CONNECTION_STRING]" --command 'create extension if not exists pg_cron'
   ```

3. Restore the roles, the schema and the data in one transaction:

   ```
   psql \
     --single-transaction \
     --variable ON_ERROR_STOP=1 \
     --file roles.sql \
     --file schema.sql \
     --command 'SET session_replication_role = replica' \
     --file data.sql \
     --dbname "[CONNECTION_STRING]"
   ```

4. Restore the Vault rows and the managed objects:

   ```
   psql \
     --single-transaction \
     --variable ON_ERROR_STOP=1 \
     --command 'SET session_replication_role = replica' \
     --file vault.sql \
     --file managed.sql \
     --dbname "[CONNECTION_STRING]"
   ```

`ON_ERROR_STOP` makes the restore stop at the first error, and
`--single-transaction` undoes everything if it does, so a failed restore leaves
your database as it was. Fix the error, then run the step again.

## Ask us to restore

If you would rather not do it yourself, email contact@pivocloud.com with the name
of your backend and the date of the backup you want. The `Contact support` link on
the `Backups` card opens an email with the backend name already in the subject. We
restore the backup and tell you when it is done, within 1 working day.

## Deleting a backend

Deleting a backend removes its backups from the `Backups` card, and you can no
longer download them. The stored copies are deleted by us afterwards. If you want
to keep your data, download a backup first. The console does not have a delete
button yet, so a deletion today is done by contacting support.
