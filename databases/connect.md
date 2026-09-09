---
title: How do I connect to my database
description: "The connection string PivoCloud gives you for a PostgreSQL database, the setting each client library needs before it accepts that string, what those settings encrypt and what they do not verify, how to pin your database's certificate, and how to turn on pgvector."
last_verified: 2026-09-09
---

## Connecting to your PostgreSQL database

This page covers PostgreSQL databases.

Your database is reachable from anywhere: your laptop, a build running on
another provider, an application you host yourself. What it needs is the
connection string PivoCloud generated for it.

One honest thing to know before you paste it anywhere. Not every client
library accepts this string unchanged, and the settings each one needs are on
this page. Read the string first, then the section for the library you use.

### The connection string

This is the shape PivoCloud hands you:

```text
postgresql://<user>:<password>@<host>:5432/pivodb?sslmode=require
```

The three values in angle brackets are yours, and they are already filled in
on the string the console shows you. Copy it whole rather than rebuilding it
by hand from the parts.

### Where to find it

Open your database from the console. The `Connection` section holds it, under
the heading `External connection string`. It stays masked until you ask for
it:

- `Reveal` fetches the string and shows it.
- `Copy` puts it on your clipboard.

The same string is what your PivoCloud apps receive when you attach this
database to them, so an app running here and a script running on your laptop
are talking to the database the same way.

### The database name is always the same

Every PostgreSQL database on PivoCloud is named `pivodb`. That name is fixed
and it is not something you pick when you create the database.

It is also not the name you typed on the creation form. That one is the label
the console lists your database under, so you can tell your databases apart.
It never reaches the server, and a client asking for it will not find it.

### The port

The port to connect on is the one in the connection string, `5432`, and
nothing else.

If you are filling in a client that wants host, port, user and password as
separate fields, take all four from the string. Any other number you see
alongside them is used internally and will not accept a connection from
outside.

## The setting your client library needs

Five clients were run against a real PivoCloud database on 2026-09-09, and
every setting below is one that connected. Each section names the exact
version that was tested, because the correct answer for a client can change
with its version, and a version you can check is what makes this page
falsifiable.

Four of the five take the string unchanged. node-postgres does not. Prisma
depends on which of its two connection paths you use: its built-in connector
takes the string as it comes, and its `PrismaPg` driver adapter runs
node-postgres underneath and inherits its answer. That difference is the
reason this section exists.

### node-postgres

**The string from the console needs one change.** Tested with `pg` 8.23.0 and
`pg-connection-string` 2.14.0.

Pasted unchanged, node-postgres refuses to connect:

```log
self-signed certificate; if the root CA is installed locally, try running Node.js with --use-system-ca
```

It reads `sslmode=require` as an instruction to verify which server answered,
which is not what that setting means in PostgreSQL itself and not something
that can succeed here. The shortest working change is one more parameter on the
end of the string:

```js
import pg from 'pg'

const client = new pg.Client({
  connectionString: process.env.DATABASE_URL + '&sslmode=no-verify',
})
```

`sslmode=no-verify` asks node-postgres to encrypt the connection without
verifying the server, which is what `require` already means to the other
clients on this page.

**One trap worth reading twice.** If you would rather pass an `ssl` object in
the configuration than change the string, you must also remove `sslmode` from
the connection string. When both are present the object is thrown away and
every setting you put on it is lost. The two halves look independent and they
are not.

### postgres.js

**The string from the console works unchanged.** Tested with `postgres` 3.4.9.

```js
import postgres from 'postgres'

const sql = postgres(process.env.DATABASE_URL)
```

No `ssl` option is needed. postgres.js reads `sslmode=require` out of the
string itself and negotiates encryption from it. That the session really was
encrypted is not the client's opinion: the server was asked directly, and it
answered TLS 1.3.

### Prisma

**The string from the console works unchanged with Prisma's built-in
connector, and needs one change with the driver adapter.** Tested with `prisma`
7.10.0, `@prisma/client` 7.10.0 and `@prisma/adapter-pg` 7.10.0.

With the built-in connector, put the string in your datasource url and change
nothing about it:

```bash
export DATABASE_URL="postgresql://<user>:<password>@<host>:5432/pivodb?sslmode=require"
```

If you use the `PrismaPg` driver adapter instead, it runs node-postgres
underneath and inherits its answer exactly, so it needs the same parameter:

```js
import { PrismaPg } from '@prisma/adapter-pg'
import { PrismaClient } from '@prisma/client'

const adapter = new PrismaPg({
  connectionString: process.env.DATABASE_URL + '&sslmode=no-verify',
})
const prisma = new PrismaClient({ adapter })
```

The node-postgres section on this page explains what that parameter does and
carries the trap that comes with it. It applies to the adapter word for word.

### psycopg

**The string from the console works unchanged.** Tested with `psycopg` 3.3.5,
built against libpq 180006.

```python
import os
import psycopg

conn = psycopg.connect(os.environ["DATABASE_URL"])
```

psycopg follows PostgreSQL's own definition of `sslmode=require`: encrypt the
connection, do not verify who answered.

### pgx

**The string from the console works unchanged.** Tested with
`github.com/jackc/pgx/v5` at v5.7.6.

```go
conn, err := pgx.Connect(ctx, os.Getenv("DATABASE_URL"))
```

pgx implements the same definition of `require` as psycopg does, which is why
the two behave identically here.

### One file on your own machine changes the answer for psycopg and pgx

Both of them follow libpq, and libpq quietly upgrades `sslmode=require` into a
verification mode when a root certificate file happens to exist at
`~/.postgresql/root.crt`. If you have that file for some other database, these
two clients start verifying, the verification cannot succeed, and a string that
works for a colleague fails for you with no obvious reason. Deleting or
renaming that file restores the behaviour described above.

## Two ways a connection fails before any of these settings matter

### Connect using the hostname, never an address

Use the hostname exactly as the connection string carries it. If you resolve
that hostname to an address and connect to the address instead, the connection
does not work.

What you get is not a certificate error. It is the connection ending with
nothing in it. From a Go client:

```log
failed to write startup message: write failed: EOF
```

From Python:

```log
SSL error: unexpected eof while reading
```

Both of those read like a network problem or a database that is down, which is
exactly why it is worth knowing in advance rather than discovering at the point
you are already looking for an outage.

### A connection that asks for no encryption

Changing the string to `sslmode=disable` does not connect. The connection is
closed. psycopg reports it as:

```log
server closed the connection unexpectedly
```

and Prisma reports it as `P1017`. Neither message says what refused it, and
this page does not guess. Leave the `sslmode` parameter as the console hands it
to you, or replace it with one of the settings above.

## What these settings do, and what they do not

Every setting above encrypts the connection. Nobody sitting between your
application and your database reads the traffic, and each of these sessions was
confirmed as encrypted by asking the server itself rather than by trusting the
client that opened it.

None of them verifies which server answered. The certificate your database
presents is generated for that one database and signed by itself, so there is no
third party inside it that a client can check it against. That is why a client
told to verify has nothing to verify with, and why setting `verify-full` on its
own does not work here.

The difference is real rather than a formality. A connection that is encrypted
but unverified is protected from being read and is not protected from being
answered by something that is not your database.

You can close that gap yourself, on your own machine, with four commands. Your
database's certificate is yours to read, and pinning it is the deliberate
version of what libpq does by accident when it finds a root certificate file.

## Pin your database's certificate

Do this once per database. `<host>` in every command below is the hostname from
your connection string, written exactly as the string carries it.

**1. Read the certificate your database presents.**

```bash
openssl s_client -starttls postgres \
  -connect <host>:5432 -servername <host> </dev/null
```

`-starttls postgres` is not optional. PostgreSQL negotiates encryption after a
short plain preamble instead of answering direct TLS, so without that flag the
command returns no certificate at all. Worse, that failing run still prints
`Verification: OK` on its way out, because nothing was verified rather than
because anything succeeded. Check that the output carries a `depth=0` line
naming your hostname before you believe any success line.

**2. Compute the fingerprint.**

```bash
openssl s_client -starttls postgres -connect <host>:5432 -servername <host> </dev/null 2>/dev/null \
  | openssl x509 -noout -fingerprint -sha256
```

The answer is one line in this shape, and yours is a different value:

```log
sha256 Fingerprint=99:03:78:8B:60:85:B4:B3:17:9C:CB:E5:91:82:50:B3:60:BF:3F:C8:63:A8:60:EA:2A:A0:A1:77:D3:F5:90:6C
```

Record it somewhere you will look again, because it is what tells you later
whether the certificate in front of you is still the one you pinned.

One limit worth stating plainly. The certificate you read in step 1 arrives
over the same unverified connection you are trying to protect. Pinning it
therefore defends every connection after the first one and not the first one
itself. If that matters to you, read the fingerprint a second time from a
different network and check that the two answers agree before you record it.

**3. Save the certificate to a file.**

```bash
openssl s_client -starttls postgres -connect <host>:5432 -servername <host> </dev/null 2>/dev/null \
  | openssl x509 -out server.crt
```

**4. Prove the pin works before you trust it.**

```bash
openssl s_client -starttls postgres -connect <host>:5432 -servername <host> \
  -CAfile server.crt </dev/null
```

The run that worked ends like this:

```log
depth=0 CN = <host>
verify return:1
Verification: OK
```

Run the same command without `-CAfile server.crt` and it ends differently:

```log
depth=0 CN = <host>
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN = <host>
verify return:1
Verification error: self-signed certificate
```

Those two answers being different is the whole point of this step. A command
that prints something reassuring either way has told you nothing.

**5. Give the saved file to your client.**

Each setting below was run with a saved certificate, and then run again against
a completely different database's certificate to check that the client really
does refuse the wrong one. Four of the five clients on this page pin, and this
list is exactly the set that was tested.

**node-postgres.** Pass the file as the trusted certificate and take `sslmode`
out of the connection string, for the reason given in its section above:

```js
import fs from 'node:fs'
import pg from 'pg'

const url = new URL(process.env.DATABASE_URL)
url.searchParams.delete('sslmode')

const client = new pg.Client({
  connectionString: url.toString(),
  ssl: { ca: fs.readFileSync('server.crt', 'utf8') },
})
```

**postgres.js.** The same file, passed in a list:

```js
import fs from 'node:fs'
import postgres from 'postgres'

const sql = postgres(process.env.DATABASE_URL, {
  ssl: { ca: [fs.readFileSync('server.crt', 'utf8')] },
})
```

**psycopg and pgx.** Both take it as connection parameters. Set `sslmode` to
`verify-full` and point `sslrootcert` at the saved file:

```text
?sslmode=verify-full&sslrootcert=server.crt
```

**Prisma's built-in connector has no pin, and it is more useful to say so than
to publish one that looks right.** With `sslmode=verify-full` and `sslrootcert`
pointing at a completely different database's certificate, it still connected.
So those parameters do not produce verification on that connector at 7.10.0.
What causes that was not established and this page does not guess at it. If you
want a verified connection from Prisma, use the `PrismaPg` driver adapter with
the node-postgres pin above, which was tested and does refuse the wrong
certificate.

### Two things break a pin

**Your database being rebuilt.** If PivoCloud has to recreate your database
somewhere else, your hostname is kept and the certificate is generated again, so
the fingerprint changes and a pinned client stops connecting. The fix is step 1
and step 3 again: read the certificate and replace your saved `server.crt`. It
is worth knowing this in advance for an unhappy reason. The moment it happens is
the moment your database has just been recovered, which is already a bad enough
day without a client refusing to connect for a reason nobody wrote down.

**A restore.** A restore gives you a new database with its own hostname, its own
credentials and its own certificate, and it leaves your original untouched. A
pin made against the original does not carry over to it: take the new connection
string from the console and pin the new certificate the same way. The
[recovery page](/databases/recovery) covers restoring itself.

## pgvector

pgvector is available on every PostgreSQL database here, whatever plan the
database is on. Nothing needs to be requested and nothing needs to be
upgraded.

It is not switched on by default, because an extension is enabled per
database. Connect to your database and run this once:

```sql
CREATE EXTENSION vector;
```

From then on `vector` columns and the similarity operators work as usual, and
the extension survives restarts and backups.
