## What I picked

Duplicate, conflicting LCP image preload on every product page in `layout/theme.liquid`.

## Why it's the highest-impact thing here

The theme had **two `<link rel=preload as=image fetchpriority=high>` blocks** firing on every PDP, with **different `imagesizes`** — so the browser preloaded two srcset variants of the same LCP image. Only one matched the actual `<img>`; the other was orphan bytes on every view.

Worse, the path-based collection preload (`request.path contains '/collections/'`) is also true on `/collections/X/products/Y` URLs, so those PDPs fired **three** high-priority preloads, racing for the LCP slot at the exact moment LCP needs it.

The two product-preload blocks even had contradicting comments (`"eliminates 1,438ms"` vs `"PDP LCP image preload. The Mo Chef trace..."`) — the git story of two devs adding preloads at different times, neither deleting the older one.

## What I did

In `layout/theme.liquid`:

1. **Deleted** the older PDP preload block — used `product.media[0]`, hard-coded `width=1200`, naive `imagesizes="(min-width: 750px) calc(100vw - 25rem), 100vw"`.
2. **Kept** the well-tuned PDP preload lower in `<head>` — uses `product.featured_image` and `imagesizes` that match the actual `<img>` sizes math in `snippets/util-product-media-sizes-attr.liquid`.
3. **Tightened** the collection-preload guard from `request.path contains '/collections/'` to `template == 'collection'`, so it stops firing on collection-namespaced PDPs.

Net diff: **2 insertions, 12 deletions**. PDPs now issue **one** fetchpriority=high preload, loading exactly the srcset variant the LCP `<img>` paints.

## What I'd do next

1. **Preload critical font woff2s** (Albert Sans 400 + 600 latin) — declared via inline `@font-face` only, so discovered late. ~50–100ms FOUT win.
2. **Reconcile Block B's `imagesizes`** with the real `<img>` math at `>=95rem` viewports (preload uses `65rem`; `<img>` uses `calc(65rem + (100vw - 95rem))`). Minor ultrawide mismatch.
3. **Lighthouse-CI on every PR** so this class of regression can't ship twice.
