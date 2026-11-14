---
title: How long are my app's logs kept, and can I search them?
description: "Runtime logs keep your app's lines for 7 days on every plan, kept in Algeria, searchable by range and text, across restarts, crashes and redeploys. What is blanked, the daily allowance, and what happens when you delete the app."
last_verified: 2026-11-11
---

## How long are my app's logs kept, and can I search them?

You can read the last 7 days of your app's runtime logs, on every plan. You can
scroll back through them, pick a time range and search them, after a restart, a
crash or a redeploy.

## Where to find it

Open your app, then the `Deployments` tab, then `Runtime logs`. The panel has two
views, `Live` and `History`. `Live` shows what your app prints now. `History`
shows what was kept. The panel opens on `Live`, except when the app is stopped,
suspended, expired, failed or unhealthy, which includes a crash loop. Then it opens
on `History`.

Reading history costs nothing and changes nothing about your app.

Build output is a different place, `Deployment logs`. It is not kept by this
feature. See [where your logs are](/apps/manage#where-your-logs-are).

## How long lines are kept

Lines your app prints are kept for 7 days, whatever plan the app is on. You can
read the last 7 days. Older lines are erased within a few hours after that. The
`Last 7 days` range is the longest one.

A single line longer than 16 KB is cut at 16 KB, and the panel marks it with
`[line shortened to 16 KB]`. A long stack trace or a large JSON text can be cut
this way.

History starts at the first line your app prints on the server that runs it. A
new app has no history until it prints its first line. A restart, a crash or a
redeploy before that first line leaves nothing in the timeline. There was no
history yet to add it to. The first line of a history reads:

```log
This app's log history starts here · Nov 10, 14:02 CET
```

## Choose a range and search

The range menu has five choices:

- `Last 15 minutes`
- `Last hour`
- `Last 24 hours`
- `Last 7 days`
- `Custom range`

`Last hour` is the default. With `Custom range` you pick a start time and an end
time, then press `Apply range`. The start cannot be older than 7 days and the end
cannot be in the future.

The search box says `Search the selected range…`. It looks for the text you type,
as plain text, and ignores upper and lower case. It looks only in the range you
picked, so pick a longer range to search further back. Search text can be up to
200 characters.

A search reads a limited amount of stored text for each request. On a busy app,
one request may not reach the start of your range. When that happens, the panel
says, with the time it reached:

```log
Searched back to Nov 10, 14:02 CET. Load older lines to search further back.
```

Press `Load older lines` to continue the search further back. When this sentence is
on screen, the search is not finished.

The newest lines are at the bottom. When a range holds more lines than one page,
older pages arrive when you press `Load older lines`. One range loads at most the
newest 5,000 lines. For older lines, pick a shorter range or search.

When a range has no lines, the panel says `No log lines in this range.` When a
search finds nothing, the panel says that no lines match your text in this range.
That answer is final only when the `Searched back to` sentence is not shown. If it
is shown, the search stopped early, so press `Load older lines` before you decide
that the text is not in your logs.

If the panel says `Log history is not available for this app yet. Live logs work
as usual.`, use `Live` in the meantime.

## What the lines in the timeline mean

Between your app's own lines, the timeline shows what happened to the app. Times
are shown in your own time zone, with its name after the time. Here are the
sentences, in the order the console lists them, each with an example time:

```log
App stopped: it exited with code 1 · Nov 10, 14:02 CET
App stopped unexpectedly · Nov 10, 14:02 CET
App restarted after a crash (exit code 1) · Nov 10, 14:02 CET
App redeployed · Nov 10, 14:02 CET
Environment variables changed, app restarted · Nov 10, 14:02 CET
App restarted · Nov 10, 14:02 CET
App rolled back to an earlier version · Nov 10, 14:02 CET
App stopped · Nov 10, 14:02 CET
Log collection resumed after a platform restart · Nov 10, 14:02 CET
This app's log history starts here · Nov 10, 14:02 CET
Log history starts here: earlier lines were not kept · Nov 10, 14:02 CET
Lines not kept from Nov 10, 14:02 CET: this app went over its daily log allowance.
Lines not kept from Nov 10, 14:02 CET: log storage was full on our side.
Lines not kept from Nov 10, 14:02 CET: they could not be checked for secrets in time.
Some lines from Nov 10, 14:02 CET to Nov 10, 14:09 CET may be missing: log collection was restarting.
```

The second sentence is the one shown when no exit code is known. The same goes
for the restart after a crash: when no code is known, it reads `exit code unknown`
in place of the number.

## Restarts, crashes and redeploys

A restart, a crash, a redeploy or a restart of our log collection does not lose or
repeat a line, except in the cases below.

In one case, the missing lines are marked. If your app printed more lines while log
collection was restarting than the server still held, the timeline shows the
`Some lines from ... to ... may be missing` sentence, with the times it covers.

In rare cases, lines can be missing with no marker. This can happen if the server
loses power, when the last few seconds of lines before it stopped may be missing.
It can also happen if the server fails to write to its storage. The history does
not show these cases.

If your app crashes while log collection itself is restarting, the timeline shows
no crash line. The lines your app printed at that time are kept.

## Times and time zones

Every time in the panel is shown in the time zone of your browser, with its name
after the time, for example `CET`. A zone with no short name is shown as an offset,
for example `GMT+4`. Two people in two time zones see the same line at two
different clock times.

The `From` and `To` fields of the custom range are your browser's own date and
time fields. They also use your time zone, but they do not show its name.

## What is blanked

Before a line is kept, we blank secrets in it, the same way the live view does:

- The value of an environment variable of your app, when the value is 8 or more
  letters or digits. A value in another alphabet, such as Arabic, can be blanked
  when it is shorter, because the length is counted in stored size.
- The value after a name and an `=`, as in `TOKEN=value`, when the name is 3 or more
  letters (or `_`) just before the `=`, and the value is 16 or more characters made
  of letters, digits and `_ - + / =`. If the value holds another character, such as
  a dot, only the part before that character is blanked.

A shorter secret is usually not blanked. A secret your app prints that is not one of
your environment variables and does not look like these shapes is kept as it was
printed. Do not print secrets in your logs.

If a line cannot be checked for secrets in time, it is not kept, and the timeline
says so with the `they could not be checked for secrets in time` sentence. The
search runs on the blanked text, so it cannot be used to find a secret.

## The daily allowance

Each app can keep about 64 MB of log lines a day. Every app has the same
allowance, whatever its plan, and it is not part of any price.

The allowance is counted in stored size, not in the size of your text. Each line
is stored with a little extra information, such as its time. Many short lines
therefore use the allowance faster than their text alone suggests.

A day starts at 01:00 Algeria time, which is 00:00 UTC. A line printed at exactly
01:00 counts against the new day.

When an app goes over its allowance, further lines are not kept until the next day
starts. The history shows one line, `Lines not kept from ... : this app went over
its daily log allowance.` The `Live` view still shows every line. If the storage
on our side is full, the history shows the `log storage was full on our side`
sentence in the same way, and lines are kept again as soon as there is room.

## Where your logs are kept

Your logs are kept in Algeria, on the server that runs your app. They are not
copied anywhere else.

That has a consequence you should know. If that server is lost, its history is
lost with it. The history then starts again on the replacement, and it says where
it starts with the sentence `Log history starts here: earlier lines were not kept`.
Log history is for reading what your app did in the last 7 days. It is not a
backup. If you need a line for longer, copy it out before it is 7 days old.

## When you delete the app

Deleting an app erases its log history. The delete is reported done only after the
history is gone. Anything your app prints while the delete is running is erased with
the rest.

## Personal data in your logs

An app's logs can carry personal data about the people who use your app. You can
read them for 7 days. We erase them within a few hours after that, and we erase
them when you delete the app. We process them only to show them to you. This page
states facts and is not legal advice. The official texts of the law are linked
from [where your data lives](/where-your-data-lives).
