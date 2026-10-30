# Minshop — No-R2 / No-card Cloudflare edition

This variant replaces Minshop's two R2 buckets with two **Workers KV** namespaces:

- `MEDIA_KV` — product images and public media
- `FILES_KV` — private digital deliverables

The business data remains in **Cloudflare D1**. The application still runs on
Cloudflare Workers/Astro.

## Why this edition exists

Cloudflare R2 requires enabling an R2 subscription/billing profile. Workers KV is
included in Workers Free, so this edition is aimed at accounts that cannot or do
not want to add a payment card.

## Cloudflare resources

`wrangler.jsonc` declares:

- D1 binding: `DB`
- KV binding: `MEDIA_KV`
- KV binding: `FILES_KV`

With modern Wrangler, missing D1/KV resources can be automatically provisioned at
deploy time.

## Storage behavior

The app already used a `StorageProvider` abstraction. This edition adds a KV
adapter and switches that abstraction from R2 to KV. Files are split into 20 MiB
chunks, below KV's 25 MiB per-value limit, so files larger than 25 MiB can still
be stored (subject to Workers request and free-plan quotas).

## Recommended Cloudflare build settings

- Build command: `npm run build`
- Deploy command: `npm run deploy:ci`
- Root directory: `/`
- Node: 22.12+ (the repo includes `.nvmrc`)

## Important free-plan limits

Workers KV Free currently has an account-wide storage allowance and daily write
limits. This is suitable for a small/medium catalog and testing, not an unlimited
media warehouse. If the store grows significantly, move the StorageProvider to a
larger object store later without changing the rest of Minshop.

## Secrets

Keep the original Minshop secrets:

- `AUTH_SECRET`
- `SECRETS_KEK`

Use different long random values for each.
