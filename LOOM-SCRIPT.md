# Loom script (≤3 min)

A tight walkthrough you can read off while sharing screen. **Don't read it word-for-word** — paraphrase so it sounds like you. The reviewers explicitly grade for whether you actually thought about this.

---

## 0:00–0:20 — Open

> "Hey, I'm \[name]. For the 2-hour task I picked one surgical fix instead of rebuilding the bundle builder, because the brief says restraint is graded — a 1-line config change that fixes a real bug beats a 500-line redesign. Let me show you what I found."

**On screen:** xinzuo.com.au PDP open in another tab (e.g., `/products/zhen-xz05-series-8-inch-chef-knife`).

---

## 0:20–1:20 — Show the bug

**Open `layout/theme.liquid`. Scroll to line 16.**

> "Here's the first LCP preload block — it fires on any URL containing `/products/`, preloads `product.media[0]` at 1200px with these `imagesizes` here."

**Scroll to line 86 (in the upstream — before your fix).**

> "And here's a SECOND preload block, this time on `template.name == 'product'`, preloading `product.featured_image` at 832px with completely different `imagesizes`. So on every product page, both fire — two `fetchpriority="high"` requests for the same LCP image, with different srcset math, the browser picks different variants for each. One of them is always wasted bandwidth at the exact moment LCP needs the slot."

**Open DevTools Network → filter `preload` → reload PDP.**

> "You can see it right here — two image preloads, both high priority. And on a `/collections/X/products/Y` URL it's even worse because the collection preload branch's `request.path contains '/collections/'` check is true on PDP URLs too, so you get a *third* preload of the first collection product that nobody's looking at."

---

## 1:20–2:20 — Show the fix

**Open the diff (or `git show` in terminal):**

> "The fix is twelve lines deleted, two added. I removed the first block — it's the older one, the `imagesizes` don't match what `util-product-media-sizes-attr.liquid` actually renders for the `<img>`. The second block's sizes math is tuned to that, so I kept it."

> "And on the collection branch I tightened the guard from `request.path contains '/collections/'` to `template == 'collection'`, so it stops firing on collection-namespaced PDP URLs."

**Show before.png / after.png briefly.**

> "Before: three high-priority preloads competing. After: one, and it's the variant the `<img>` actually paints."

---

## 2:20–3:00 — Why & what's next

> "Why this one? It's invisible in source review — both blocks look 'correct' on their own. You only see the bug when you read them together on the same page. That's the kind of catch I'd want a teammate to make."

> "Next I'd preload the two latin Albert Sans woff2s — they're declared inline in `google-fonts.liquid` so the parser discovers them late. And I'd put Lighthouse-CI on every PR so the next dev who adds a 'better' preload doesn't accidentally ship the duplicate again."

> "Thanks!"

---

## Tips

- Keep your face cam in a corner, screen takes 80%.
- Don't apologize for what you *didn't* do. Stay on the one thing.
- If you go over 3:00, re-record. The brief is strict on this.
- Paste the Loom URL into `README.md` before pushing.
