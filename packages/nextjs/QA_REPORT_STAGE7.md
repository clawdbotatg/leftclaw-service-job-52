# Stage 7 -- Frontend QA Audit Report

**Job:** #52 -- CLAWD Token Dashboard
**Date:** 2026-04-13
**Auditor:** Stage 7 independent QA (Opus subagent)
**Scope:** Source code + static build output in `packages/nextjs/`

---

## Ship-Blockers

| # | Item | Verdict | Notes |
|---|------|---------|-------|
| 1 | Wallet connect: no connect button / no wallet text | **PASS** | Header, Footer, and page.tsx contain zero wallet/connect references. No `RainbowKitCustomConnectButton` rendered anywhere in user-facing code. |
| 2 | Wrong network: N/A (no contracts) | **N/A** | -- |
| 3 | Approve button: N/A (no transactions) | **N/A** | -- |
| 4 | Contracts verified: N/A (no contracts) | **N/A** | -- |
| 5 | SE2 footer branding removed | **PASS** | Footer.tsx is fully replaced -- shows "CLAWD Token Dashboard" with DexScreener attribution. No BuidlGuidl, Fork me, Support links. No `nativeCurrencyPrice` badge, no `FaucetButton`, no `SwitchTheme`. |
| 6 | SE2 tab title removed | **PASS** | `getMetadata.ts` line 8: `titleTemplate = "%s"` (not `"%s | Scaffold-ETH 2"`). Built HTML title is "CLAWD Token Dashboard". |
| 7 | SE2 README replaced | **FAIL** | `README.md` at repo root is the **stock SE2 template README** -- references "Scaffold-ETH 2" 8 times, links to SE2 docs, shows Debug Contracts screenshot. Must be replaced with CLAWD project content. |
| 8 | Favicon replaced | **PASS** | `public/favicon.svg` is a custom CLAWD lobster SVG (dark background, red lobster with claws). `public/favicon.png` is a matching CLAWD lobster raster image. No SE2 crane/scaffold default. `getMetadata.ts` references `favicon.svg`. |

---

## Should-Fix

| # | Item | Verdict | Notes |
|---|------|---------|-------|
| 1 | Contract address with copy + BaseScan link | **PASS** | Address displayed in monospace with copy-to-clipboard button (with fallback for `execCommand`) and clickable BaseScan link (`https://basescan.org/token/0x9f86...`). Not using SE2 `<Address/>` component, which is appropriate since there is no wallet context and this is a pure display -- custom implementation satisfies the spec requirement of "copy-to-clipboard button and link to BaseScan". |
| 2 | OG image uses absolute URL | **FAIL** | `getMetadata.ts` correctly constructs absolute URL using `NEXT_PUBLIC_PRODUCTION_URL`. However, the built HTML shows `https://clawd-dashboard.example//thumbnail.jpg` -- a placeholder domain that does not resolve. Also note the double slash (`example//thumbnail.jpg`). After IPFS deploy, `NEXT_PUBLIC_PRODUCTION_URL` must be set to the real IPFS URL and the build must be regenerated for the OG image to work. The double slash (trailing slash on base URL + leading slash on path) should also be fixed in `getMetadata.ts`. |
| 3 | `--radius-field` changed to `0.5rem` | **PASS** | Both light and dark theme blocks in `globals.css` set `--radius-field: 0.5rem`. Confirmed no `9999rem` in compiled CSS output. |
| 4 | Token amounts have USD context | **PASS** | DexScreener returns USD price directly. Price displayed with `$` prefix via `formatPrice()`. 24h change shown as percentage. |
| 5 | Errors mapped to human-readable messages | **PASS** | Error state shows "Price unavailable" (line 229 of page.tsx). On fetch failure, `setError(true)` and `setPriceData(null)` clear stale data. |
| 6 | Phantom wallet in RainbowKit wallet list | **PASS** | `wagmiConnectors.tsx` line 7: `phantomWallet` imported and included in wallets array at line 27. Dormant since no connect button is rendered but correctly defined. |
| 7 | Mobile deep linking | **N/A** | No writes / no transactions. |
| 8 | `appName` in `wagmiConnectors.tsx` | **PASS** | Line 51: `appName: "CLAWD Token Dashboard"` (not `"scaffold-eth-2"`). |

---

## Spec-Specific Checks

| # | Item | Verdict | Notes |
|---|------|---------|-------|
| 1 | DexScreener API URL exact match | **PASS** | Line 8: `https://api.dexscreener.com/latest/dex/tokens/0x9f86dB9fc6f7c9408e8Fda3Ff8ce4e78ac7a6b07` -- correct. |
| 2 | Auto-refresh every 30 seconds | **PASS** | Line 77: `setInterval(fetchPrice, 30000)` -- correct. |
| 3 | Interval cleaned up on unmount | **PASS** | Lines 78-80: `useEffect` returns cleanup function that calls `clearInterval(intervalRef.current)`. Uses `useRef` to store interval ID. |
| 4 | Positive 24h change: green color | **PASS** | Line 247: `color: isPositive ? "#4ade80" : "#C41E3A"` -- green `#4ade80` for positive. |
| 5 | Negative 24h change: deep red `#C41E3A` | **PASS** | Same line -- `#C41E3A` for negative. Not cream/text color. |
| 6 | Fallback "price unavailable" on error | **PASS** | Lines 226-230: `error || !priceData` renders "Price unavailable". `setPriceData(null)` in catch block clears stale data. |
| 7 | Dark red lobster theme colors | **PASS** | Background `#0a0a0f` (line 107 page.tsx, line 92 globals.css), primary red `#8B1A1A` and `#C41E3A` throughout, text `#F5E6E0`. Card has glow: `box-shadow: 0 0 40px rgba(196, 30, 58, 0.08)`. Lobster glow animation in globals.css uses `rgba(196, 30, 58, ...)`. |
| 8 | Monospace for address/price, sans-serif for labels | **PASS** | Address uses `fontFamily: "var(--font-mono), monospace"` (line 191). Price uses same (line 238). Labels use default sans-serif from Inter font. |
| 9 | `public/thumbnail.jpg` is CLAWD-branded | **PASS** | Inspected: shows CLAWD lobster icon with "CLAWD", "Token Dashboard", "on Base" text on dark background with red glow. Not SE2 default. |
| 10 | `public/manifest.json` has CLAWD name | **PASS** | `"name": "CLAWD Token Dashboard"`, `"short_name": "CLAWD Dashboard"` -- not SE2 default. |
| 11 | `public/logo.svg` is lobster/CLAWD | **PASS** | Custom CLAWD lobster SVG with dark background, red claws, and eyes. Not SE2 crane. |
| 12 | No "Scaffold-ETH" strings in `out/` | **PASS** | Grep for `scaffold-eth-2`, `Scaffold-ETH`, `scaffoldeth`, `BuidlGuidl` in `out/` returned zero matches. |
| 13 | No `blockexplorer` or `debug` routes in `out/` | **PASS** | Neither `out/blockexplorer` nor `out/debug` directories exist. Source directories correctly renamed to `_blockexplorer-disabled` and `_debug-disabled`. |

---

## Additional Observations

| # | Item | Severity | Notes |
|---|------|----------|-------|
| A1 | ProgressBar color is blue `#2299dd` | Low | `ScaffoldEthAppWithProviders.tsx` line 51 uses SE2 default blue. Should be `#C41E3A` (CLAWD red) for theme consistency. Not a ship-blocker. |
| A2 | `wagmiConfig.tsx` has bare `http()` fallback | Low | Line 30: `rpcFallbacks = [http()]` as last-resort fallback. This hits public RPCs. Since this dashboard makes no onchain calls (price data comes from DexScreener API, not RPC), this is dormant. But for correctness, the fallback should be removed. Not a ship-blocker for this read-only dashboard. |
| A3 | OG image double-slash in URL path | Medium | `getMetadata.ts` concatenates `${baseUrl}${imageRelativePath}` where `baseUrl` may have trailing slash and `imageRelativePath` starts with `/`. Result: `example//thumbnail.jpg`. Should strip trailing slash from `baseUrl` or leading slash from path. |

---

## Verdict: FIXES NEEDED

**FAIL items that must be fixed before Stage 9:**

1. **Ship-blocker #7 -- README.md** is still the stock SE2 template. Must be replaced with CLAWD Token Dashboard project description.
2. **Should-fix #2 -- OG image URL** uses placeholder domain `clawd-dashboard.example` and has a double-slash bug. After IPFS deploy, the `NEXT_PUBLIC_PRODUCTION_URL` must be set to the real CID-based URL and the build regenerated. The double-slash in URL concatenation (`getMetadata.ts`) should also be fixed.

**Recommended fixes (not blocking but should address):**

3. ProgressBar color `#2299dd` (blue) should be changed to `#C41E3A` (CLAWD red) in `ScaffoldEthAppWithProviders.tsx`.
4. Bare `http()` fallback in `wagmiConfig.tsx` should be removed (dormant but incorrect).
