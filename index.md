---
title: How do I start using PivoCloud?
description: "Sign up, verify your email, complete your profile and claim the 1,200 DA starting credit, then follow three steps to a running app: check your repository, connect GitHub, deploy it."
last_verified: 2026-09-06
---

## Get started

PivoCloud builds your app from your own Dockerfile, runs it on infrastructure in
Algeria, and bills you in dinars. This page takes you from a new account to a
deployed app.

### 1. Sign up and verify the email

Register in the PivoCloud console with an email address and a password. The
submit button is `Create account`.

The panel that follows says `Registration successful!` and asks you to check
your email for a verification link. Click that link before you do anything
else. Signing in is gated on the email being verified, so an account whose link
has not been clicked cannot log in at all. If the message never arrives, the
same panel carries a `Resend verification` link that sends it again.

### 2. Complete your profile and claim the credit

A new account starts with a wallet balance of zero. The 1,200 DA is not granted
automatically at signup. You claim it, and a person approves the claim.

Two steps, in this order.

**Fill in your profile.** On the `Profile` page, fill `First name`, `Last name`
and `Phone number`, then submit `Save profile`. All three are required before a
claim is possible. While any of them is empty, the button on the dashboard
banner reads `Complete Profile` and brings you to this page.

`Phone number` has to be an Algerian mobile: ten digits beginning `05`, `06` or
`07`, in the shape the field's own placeholder shows, `e.g. 0555 12 34 56`.
International notation for the same number is accepted, so there is nothing to
guess: a leading `+213` is understood, and spaces and hyphens between the digits
are ignored. Anything else is refused with
`Enter a valid Algerian mobile number (05, 06, or 07)`, and the profile stays
incomplete until it is fixed.

**Claim the credit.** Once the profile is complete, the dashboard banner's
button reads `Claim Now`. The `Wallet` page carries the same action on its
`Free Credits` card, where the button carries the amount instead and reads
`Claim 1,200 DA`. Either one works, so click it once.

Then expect a wait. The approval is not automatic: someone at PivoCloud reviews
the claim by hand, so the credit does not appear the moment you click. While the
claim is pending, the dashboard banner hides itself entirely and the dashboard
shows nothing about it. The `Wallet` page in the sidebar is the only place the
pending claim is visible. Look there. A quiet dashboard does not mean the claim
failed.

### 3. What 1,200 DA buys

The credit covers one month of `Lite` hosting for one app, at 1,200 DA per app
per month, or one month of a `Starter` database at 1,200 DA per month. The full
catalogue, including the larger app plans and database tiers, is on the
[pricing page](https://pivocloud.com/pricing).

### 4. The three things to do first

In this order. The failure that costs a new customer their first hour is a
repository that cannot build, so the check comes before everything else.

**1. Check that your repository meets the deployment contract.** PivoCloud
builds from the Dockerfile in your repository and reads the first `EXPOSE` line
in it to know which port to reach your app on. Six rules decide whether a
repository deploys, and most first deploys that fail, fail on the first two.
Read [what your repository needs](/apps/deployment-contract) before you create
an app, and fix anything it turns up while you still have an empty account.

**2. Connect your GitHub account.** PivoCloud pulls your code through the
PivoCloud GitHub App, which you install once and point at the repositories you
want it to see. Private repositories work through that connection, and so does
deploying on every push.
See [connecting GitHub](/apps/connect-github).

**3. Deploy it.** Create the app, pick the repository and the branch, choose the
subdomain your app answers on, and watch the build. If your app needs API keys,
database URLs or any other configuration, set them as environment variables on
the app before or after the first deploy; changing one replaces the container,
so the new value is live within a deploy.
See [deploying your first app](/apps/deploy).
