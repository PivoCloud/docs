---
title: How do I connect GitHub to PivoCloud?
description: "Install the PivoCloud GitHub App, see exactly which repositories PivoCloud can read and why, including private and organisation-owned ones, and change that set from GitHub whenever you need to."
last_verified: 2026-09-06
---

## Connect GitHub

PivoCloud pulls your code through the PivoCloud GitHub App. You install it once,
you choose which repositories it may read, and every app you create afterwards
picks from that set.

Private repositories work through this connection. So does deploying on every
push.

### Install the App

Open `Integrations` in the console sidebar.

Before you connect, that page shows one heading, `No GitHub connection`, the
line `Connect the PivoCloud GitHub App to deploy private repositories and enable
automatic push-to-deploy.`, and one button: `Connect GitHub`.

Press it and you land on GitHub, where the App installation is set up. GitHub
asks the question that decides everything on this page: **all repositories, or
only the ones you select.** Answer it, confirm the install, and GitHub sends you
back to the console.

Once you are back, `Integrations` shows a `GitHub App` card carrying a
`GitHub connected` badge, a count of the repositories the installation can
reach, and the GitHub account it belongs to. Under the card, an
`Authorized Repositories` section lists them one by one. Two links sit on the
card: `Manage on GitHub`, which opens this installation's settings on GitHub,
and `Disconnect`.

Start from the console's own `Connect GitHub` button rather than from a
bookmarked GitHub address. The console builds that link for the environment you
are signed in to, so it always points at the right App.

### Which repositories PivoCloud can see

The set is exactly the repositories your GitHub App installation is authorized
for. Nothing more, nothing less. That is a choice you make on GitHub at install
time, and you can change it whenever you like.

PivoCloud adds no filter of its own. It does not ask GitHub for public
repositories, or for personal ones, or for recently updated ones. It asks the
installation for its repositories and lists what comes back.

Four cases come up, and all four are normal.

**A private repository appears like any other.** Every row carries a badge,
either `Private` or `Public`. Being private is never the reason a repository is
missing: reading private code is what this connection exists for.

**Repositories owned by an organisation sit on the same footing as your own,**
as long as the installation covers them. Installing on an organisation is a
separate install from installing on your personal account, and some
organisations require an owner to approve the request before it takes effect.
That approval happens on GitHub. PivoCloud has no part in it and cannot hurry
it.

**A long list is a complete list.** If your installation covers hundreds of
repositories, you see hundreds of them. There is no cut-off, no first-page-only
behaviour, and nothing you need to click to reach the rest. If a repository you
authorized is not there, the selection on GitHub is what to look at, not the
length of the list.

**An empty list means the installation is authorized for nothing.** The picker
says `No repositories found. Check your GitHub App permissions.` Read that as a
question about the repository selection rather than about permissions in
general: open the installation on GitHub and look at which repositories you
granted it. An App installed with "only select repositories" and an empty
selection produces exactly this message.

### Changing which repositories are visible

The list is read from GitHub every time the page loads. PivoCloud keeps no copy
of it, so there is nothing stale to clear.

Three steps, and the third is the one people miss:

1. Open `Manage on GitHub` on the `Integrations` page. It takes you straight to
   this installation's settings.
2. Change the repository selection there, on GitHub.
3. Come back to PivoCloud and load the page again.

The console has no control for re-reading the list, and it does not need one:
loading the page **is** the re-read. If you changed the selection on GitHub and
the old set is still on screen, you are looking at a page that was loaded before
you made the change.

### When the page shows a button and no error

If your account has no installation, or the installation was removed or
suspended on the GitHub side, the console shows the `Connect GitHub` button and
says nothing at all. No error, no warning, no explanation.

That silence is deliberate rather than broken. A brand-new account and a lapsed
installation look identical from here, and the first of those is not a failure
worth alarming anyone about. The practical reading is simple: if you expected to
be connected and you are looking at that button, you are not connected. Install
the App again from it.

### Disconnecting

`Disconnect` sits on the `GitHub App` card. The confirmation asks
`Disconnect GitHub App?` and tells you what it costs:
`Your deployed apps will keep running. Auto-deploy will stop until you
reconnect.`

So disconnecting is not a way to take an app down. Anything already deployed
keeps serving traffic; what stops is PivoCloud's ability to read your code,
which means no automatic deploy on push and no new build until you connect
again.

### Where you actually pick the repository

Not on this page. The picker lives on the create-app form: a control labelled
`Pick a repository`, with a `Search repositories…` field inside it once it
opens. Everything on this page decides what that picker contains.

For the rest of that form, the branch, the build settings, the plan and the
subdomain, see [deploying your first app](/apps/deploy).
