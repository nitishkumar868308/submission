# Xinzuo — TYICDI 2-hour task

**The fix:** removed a duplicate, conflicting LCP image preload from `layout/theme.liquid`. Every PDP previously fired two `fetchpriority="high"` `<link rel="preload" as="image">` blocks with different `imagesizes`, so the browser preloaded two different srcset variants of the same LCP image — one of them always wasted. On `/collections/X/products/Y` URLs the bug compounded into three competing preloads. The fix deletes the older block and tightens the collection-preload guard so it stops firing on PDPs.

Net diff: **2 insertions, 12 deletions** in one file. See [NOTE.md](NOTE.md) for the full reasoning.

## Loom

**TODO — paste your Loom URL here before submitting:** `<loom-url-here>`

(Max 3 minutes, face + screen. See `LOOM-SCRIPT.md` for what to walk through.)

## Files in this repo

| File | What it is |
|---|---|
| [`layout/theme.liquid`](layout/theme.liquid) | The single edited theme file — diff cleanly against the upstream at `dintyo/xinzuo-theme-snapshot`. |
| [`before.png`](before.png) | Bug: three high-priority preloads racing on one PDP. |
| [`after.png`](after.png) | Fix: one preload, matching the LCP `<img>`. |
| [`NOTE.md`](NOTE.md) | What I picked / why / what I did / what's next (≤300 words). |
| [`LOOM-SCRIPT.md`](LOOM-SCRIPT.md) | 3-minute walk-through script for the Loom. Not part of the brief — delete before submitting if you want. |

## How to verify the fix

1. Clone the upstream and push the theme to your own Shopify dev store (one-command setup in the upstream README at <https://github.com/dintyo/xinzuo-theme-snapshot>).
2. Replace `layout/theme.liquid` with the version in this repo, push the theme again.
3. Open any PDP, e.g. `/products/zhen-xz05-series-8-inch-chef-knife`.
4. DevTools → Network → filter `preload` → before: 2 image preloads at high priority; after: 1.
5. Lighthouse → "Preload Largest Contentful Paint image" goes from a warning to a pass.

> Screenshots in this repo are DevTools-style mockups (allowed per the brief). For a real-store screenshot, run the steps above and capture the Network panel yourself.

## Credit

Upstream theme © Xinzuo Australia / Told You I Could Do It — used here solely for the TYICDI hiring task per the upstream license.
