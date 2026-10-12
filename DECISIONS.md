# Design decisions

**Status:** design documentation. These are decisions in the approved architecture baseline (version 2.9), together with product decisions made on 2026-10-12. They describe how the platform is designed to work. None of it is built yet.

Each entry says what was decided and what it means in practice. For the terms used here, see the [glossary](https://github.com/Faka-iAction/docs-public/blob/main/GLOSSARY.md). For the mechanisms, see [OVERVIEW.md](OVERVIEW.md).

Rates, limits, period lengths and risk rules are configuration. They are not published here. The detailed rules for bids will be published with the contracts.

## 1. There is no wallet

No member, guest or creator holds a spendable, top-up or transferable balance on the platform, and no Action can be paid for from a platform balance. A creator sees a pending balance: a read-only figure calculated from the ledger. This means the platform never stores a balance as an editable number, there is no "withdraw" operation, and the ledger stays the only financial record. Payments are collected and held by a licensed payment provider. FAKA iAction does not hold customer funds.

## 2. Every collected payment passes through a cool-off before settlement

A payment collected through the payment provider is not paid out to the creator straight away. It waits for a mandatory cool-off period, during which the provider holds the funds. The cool-off is always applied; only its length is configurable. This means refunds, disputes and reversals are paid from money that has not yet reached the creator, and the platform does not have to recover money that has already been paid out.

## 3. Unfulfilled paid Actions are refunded automatically

Every paid Action has a fulfilment deadline, shown to the participant before payment. If the creator declines the Action, or has not marked it fulfilled when the deadline passes, the payment is refunded in full without the participant asking. The deadline always falls inside the cool-off, so the refund comes from funds still held by the provider. No platform fee is kept on an Action refunded this way. A Tip has no fulfilment step, so this rule does not apply to it.

## 4. A creator can sell before setting up payout details, but is not paid until they do

A creator can publish paid Actions before configuring a Settlement Profile, which is the creator's verified payout details. Payments are collected and held by the provider, and the creator sees them as a pending balance. Nothing is paid out until the Settlement Profile is verified. The cool-off passing does not release funds on its own. This lowers the effort needed to start, without paying money to an unverified destination.

## 5. Cash is recorded, not processed, and only for a creator who manages their own profile

Cash handed to a creator in person is a direct payment between two people. The platform records it only when the creator, signed in, confirms they received it. No provider transaction is created, the platform charges no fee on it, and the amount never enters a pending balance or a payout. Cash has its own state labels so it cannot be mistaken for a provider payment. Only the creator who owns the profile can confirm cash.

## 6. Action, payment and settlement are three state machines

There is one canonical set of payment states and one canonical set of settlement states. A provider's own states are mapped onto them. Refund and dispute are payment states, not Action states. An Action has its own state machine per Action type. This means a single interaction can be described accurately at any moment, for example "fulfilled, payment confirmed, not yet settled", and no part of the system reads a payment status as proof of delivery.

## 7. Tip is the fifth Action type in the first release

The first release has five Action types: Request, Vote, Bid, Shout-out and Tip. A Tip is a voluntary amount with nothing owed in return. It is complete when its payment is confirmed. It still passes through the cool-off and the same payment controls as any other payment. Donation is a separate, later Action type with its own rules, and is not offered through Tip. A Bid is a paid offer for priority, ranked against other bids. Votes are free in the first release, and a vote cannot be changed once it is submitted.

## 8. One shared database; the Symfony implementation owns the schema

All four backend implementations use one authoritative database and one schema. The Symfony implementation is the only one that creates and runs schema migrations. The Spring Boot, ASP.NET Core and FastAPI implementations map onto the existing schema, implement the API only, and never migrate. This means there is no data synchronisation between backends, and there is exactly one place where the schema can change.

## 9. The pilot comes before the other three backends

The plan is to build the Symfony implementation first, take it through production operations, and use it for a pilot with a small group of creators. The other three backends will follow the pilot, written against contracts and conformance tests that the pilot has exercised. All four remain required. This means the first release is not delayed by parallel implementations, and the later ones will be written against a contract that has met real use.

## 10. The web is server-rendered; the mobile app is Flutter

Symfony with Twig templates serves the whole web experience, public and signed-in. Flutter is the mobile app for Android and iOS. There is no Flutter web build and no installable web app. Web controllers and API controllers call the same shared business modules, so both clients apply the same rules. The mobile app must match the web in what a user can do; it adds only device features such as camera scanning and push notifications.

## 11. A printed QR code encodes a short opaque identifier

A QR code does not contain a creator's handle or an Action's address. It contains a short identifier that the server resolves and redirects to the canonical page. This means a creator can rename their handle, change the Action a code points to, or replace a code, and codes that are already printed keep working. A code can also be suspended or revoked. After a rename the old handle also keeps working: it redirects to the new handle and is not given to anyone else.

## 12. Sign-in by email, phone number or social login

A member can register and sign in with an email address, with a phone number, or through a social login. All three lead to one member identity. A guest needs none of them to take part. This means a person can use whichever identifier they already have, and registration stays optional for the audience.

## 13. One creator profile per member

A member has at most one creator profile. This means a creator has one public page, one handle and one set of Creator Actions, and the audience always knows which profile a code or link belongs to.
