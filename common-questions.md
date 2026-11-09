---
title: Common questions before you start
description: "Straight answers to what people ask before they move an app or a database to PivoCloud, with a link to the one page that holds each fact."
last_verified: 2026-11-09
---

These are the questions people ask most before they move an app or a database to PivoCloud. Each answer links to the page that holds the details.

## Running in production {#production}

PivoCloud runs in two datacenters in Algeria. We test restores regularly, and pivocloud.com shows the latest result when there is one. If your app’s main process stops, your app restarts by itself. You reach support by phone, WhatsApp or email, and the pricing page shows how fast each plan gets a first reply.

Read about the [weekly restore drill](/databases/recovery#weekly-restore-drill), [what happens at a plan’s limits](/plans-and-limits), [how to get help](/getting-help) and the [pricing page](https://pivocloud.com/pricing).

## How your data is protected {#data-protection}

Connections to your database require TLS. Your app’s environment variables are stored encrypted. Database backups are encrypted before they are stored.

Read how to [connect to your database](/databases/connect) and how [environment variables](/apps/environment-variables) work.

## Prices, invoices and trying before you pay {#prices-and-trial}

The monthly price shown when you buy is what is taken from your balance each month, and nothing is added to it. If your company needs an invoice, write to us. A new account can claim free credit once its profile is approved, and the pricing page shows the amount. If you need longer to check that your whole stack runs, write to us.

See the [pricing page](https://pivocloud.com/pricing), [credit and charges](/billing/credit-and-charges) and the [contact page](https://pivocloud.com/contact).

## Papers for your company {#papers}

On request we sign a service contract, a written attestation that your databases, apps and backups are hosted in Algeria, and a data processing agreement. The data residency page lists what is held in Algeria and what is not.

See the [data residency page](https://pivocloud.com/data-residency-algeria), [where your data lives](/where-your-data-lives) and the [contact page](https://pivocloud.com/contact).

## What we do not offer today {#not-offered}

PostgreSQL is the only database we run today. There is no object storage and no persistent disk for apps today. Anything your app writes to its own disk is gone when it is deployed again, so keep files in your database or in a storage service you choose. Today there is no view of what your app or your database consumes.

Read [the deployment contract](/apps/deployment-contract).

## Exporting your data and leaving {#leaving}

You can export your database from the console and download the file before it expires. Exports run one at a time.

Read about [exports](/databases/recovery#exports-and-downloading-one).
