## What I picked

Duplicate LCP image preload on every product page in `layout/theme.liquid`.

## Why it's the highest-impact thing here

Every PDP fired two fetchpriority=high image preloads with different imagesizes. The browser fetched two srcset variants of the same LCP image — one was always wasted. On /collections/X/products/Y URLs a third path-based collection preload also fired, racing three high-priority requests for the LCP slot at the worst possible moment.

Two contradicting comments in the same file (one claims "eliminates 1,438ms", the other "Mo Chef PDP trace...") tell the story: two devs added preloads at different times, neither removed the older one. Invisible reading either block alone; obvious only when read together.

## What I did

1. Deleted the older PDP preload (used product.media[0], naive imagesizes, hard-coded width 1200).
2. Kept the well-tuned PDP preload below — its imagesizes match the real img sizes math in util-product-media-sizes-attr.liquid.
3. Tightened the collection-preload guard from a path-contains check to template == 'collection', so it stops firing on collection-namespaced PDPs.

Net: 2 insertions, 12 deletions. One preload per PDP, matching the variant the LCP img actually paints.

## What I'd do next

1. Preload critical font woff2s (Albert Sans 400/600 latin) — declared inline only, discovered late.
2. Reconcile the kept preload's imagesizes with the real img math at very wide viewports.
3. Lighthouse-CI on every PR so this class of regression can't ship twice.
