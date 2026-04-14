# CLAWD Token Dashboard

A live price dashboard for the CLAWD ERC-20 token on Base.

**Contract:** `0x9f86dB9fc6f7c9408e8Fda3Ff8ce4e78ac7a6b07`

No wallet connection needed -- this is a read-only dashboard.

## Features

- Live CLAWD price in USD via DexScreener API
- 24h price change with color-coded indicator
- Auto-refresh every 30 seconds
- Copy-to-clipboard for contract address
- Direct link to BaseScan token page
- Dark red lobster abyss aesthetic

## Run locally

```bash
yarn install
cd packages/nextjs
yarn start
```

Then open http://localhost:3000

## IPFS build

```bash
cd packages/nextjs
NODE_OPTIONS="--require ./polyfill-localstorage.cjs" \
  NEXT_PUBLIC_IPFS_BUILD=true \
  NEXT_PUBLIC_IGNORE_BUILD_ERROR=true \
  yarn build
```

Output goes to `packages/nextjs/out/`.

## Price source

Price data is fetched from the [DexScreener API](https://api.dexscreener.com/latest/dex/tokens/0x9f86dB9fc6f7c9408e8Fda3Ff8ce4e78ac7a6b07) and refreshed every 30 seconds.
