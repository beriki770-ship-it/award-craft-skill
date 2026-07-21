---
name: award-craft
description: Build websites at Awwwards Site-of-the-Day level that also pass the accessibility and performance bar most winners skip. Use when building or reviewing any site that should feel award-grade — hero interactions, scroll choreography, WebGL moments, kinetic type, preloaders — or when someone asks "make this feel like an awwwards site". Grounded in a July 2026 dissection of six SOTD winners and the official jury criteria.
---

# award-craft — win where the winners are weak

Built from a real dissection: six Awwwards Site-of-the-Day winners (July 2026), the official evaluation system, and the developer-jury sub-scores behind them. Proof of concept built with this playbook: https://digital.wildmoments.at/wow/ — an essay that demonstrates each rule and measures itself live.

## The strategy in one paragraph

Awwwards scores Design 40% / Usability 30% / Creativity 20% / Content 10%, with 18+ jurors and outliers dropped. Winners land around 7.2–7.5 overall. Their animation craft scores 9+. Their accessibility scores ~6 and usability ~7.1. So the path past them was never a bigger shader — it's matching their motion craft while doing the work they skip: keyboard, contrast, load time, layout stability. A page that animates like a winner AND measures LCP < 1.5s, CLS < 0.05, INP < 100ms with a real reduced-motion build has no competition in that combination.

## What every winner shares (steal this structure)

1. **One signature mechanic, not five.** Lacoste: a polo you spin. RISK: one endless corridor. Hiroto Sato: a sticker you peel. Pick the single moment a visitor will retell, make everything else quiet craft. Juries praise 2–4 highlighted elements, never "the whole page".
2. **A two-color palette.** Cyan/cream, black/cream, rust/near-black, white/near-black — motion carries the interest, color stays out of the way. One accent with a real job; radius and spacing decided, not defaulted.
3. **The preloader is a designed beat**, scored as animation craft: counter (000→100) + clip-path wipe is the current standard shape. Gate it on a real asset (fonts, scene), cap ~1.5s, skip on repeat visit via sessionStorage, remove entirely for reduced-motion. A preloader with nothing loading behind it is latency dressed as brand.
4. **GSAP is table stakes, not an edge.** Every winner runs it — and since GSAP went fully free (SplitText included, v3.13+), the paid-plugin moat is gone. Differentiation moved entirely to taste: small staggers (0.06s, not 0.1), sub-second easings, one rhythm per page.
5. **The stack doesn't matter.** Webflow + GSAP + a no-code WebGL layer scores the same 7.1–7.3 band as bespoke Vue + Three.js. Backends are boring and invisible. Spend the budget on the front-end craft layer.
6. **Interior pages, 404, forms, loading states get hero-level care.** Inconsistency between the homepage and everything else is an explicit jury red flag.

## Technique recipes (2026 tier)

- **The one-clock pattern** — THE mechanism behind GSAP+WebGL award sites: feed ScrollTrigger's 0–1 progress straight into a shader uniform (`scrub: true`). DOM and GPU share one clock; scrolling back runs everything in reverse. No separate sync layer.
- **Line-mask reveals** — the universal "content enters" primitive: wrap each SplitText line in `overflow: hidden`, tween `yPercent` 110 → 0, `expo.out`, stagger ~0.09.
- **Kinetic type without layout shift** — variable-font weight responding to the cursor normally pushes sibling glyphs and racks up CLS. Fix: split words+chars, absolutize positions first (SplitText `position: "absolute"`), lock the container height, then morph `wght` freely. Letters swell; nothing moves. Verified CLS 0.000. On touch devices the finger is the cursor (touchmove on the headline, passive) — and since nobody knows a headline is touchable, run ONE weight-wave through the letters after the intro to teach it.
- **Magnetic elements via `gsap.quickTo()`** — per-frame `gsap.to()` calls create tween garbage; quickTo exists exactly for this. One magnetic element per page, radius ~120px, pull ~0.3. Sitewide magnetism reads as noise by the second visit.
- **Scrub reveals over time-based reveals** for anything below the fold — a fast scroller outruns entrance animations and sees blank sections; scrub-linked reveals can't be outrun, and they replay honestly in both directions.
- **Lazy WebGL** — boot the context via IntersectionObserver near the viewport, render at ~0.7× device pixels and let CSS upscale, pause on tab-hidden and off-screen with ONE guarded rAF chain (an unguarded IO-enter callback quietly stacks parallel render loops). Ship a CSS poster behind the canvas so no-WebGL and reduced-motion get a composed frame, not a hole.
- **Compositor discipline** — transform, opacity, clip-path, filter only for anything continuous. Changing a CSS custom property per frame can force style recalc across the whole tree (`@property { inherits: false }` or write `element.style.transform` directly). One honest exception is allowed if it's the signature (e.g. font-variation on a dozen letters) — then say so.
- **Reduced motion is a second build, not zero-duration tweens** — bars pre-filled, counters at final values, shader static, pin removed. Branch at init, and keep the no-JS path real too: final states in HTML/CSS, JS only ever winds them back to animate forward.
- **Native first** — View Transitions for page changes unless the transition IS the brand moment; scroll-driven CSS animations for secondary reveals; smooth-scroll hijacking (Lenis et al.) is optional, not required, and skipping it is a usability point in your pocket.

## Now cliché — do not ship (2026)

Film-grain overlay on everything · purple-to-blue gradients · custom cursor sitewide · horizontal scroll-jack without a spatial reason · word-by-word scrubbed reveals on full paragraphs (short statements only) · "because we can" fluid/particle backgrounds unrelated to content · preloaders gating nothing · auto-rotating carousels · low-contrast oversized display type. Also retired by overuse: Inter-only typography, and yes — Fraunces.

## The eight build rules

1. Usability is 30% of the score and the #1 reason pretty sites lose — design it in from day one.
2. One point of view, carried by type, color and motion together. A trend collage scores as no view at all.
3. Performance budget lives beside the design: LCP < 1.5s, CLS < 0.05, INP < 100ms, < 3MB, 60fps on a mid-range phone — tested throttled, not on the dev machine.
4. Mobile is designed in parallel, never as a squeeze pass. Pins and stages that don't fit small screens get a different build, not a scrollbar.
5. Every animation has a job: pace the story, reveal information, or reward a gesture. No job, no animation.
6. The 404 gets the same care as the hero. Juries check.
7. Real, finished words. Placeholder copy poisons every category, not just Content — and every number on the page should survive a click to its source.
8. Accessibility is the cheapest way to outscore a winner: designed focus rings, a skip link, AA contrast even for small accent-colored labels, live regions that don't spam, aria-hidden on the decorative layer.

## Hard-won Lighthouse lessons (from taking the proof page to 100/100/100/100 + Agentic 2/2)

- **Fades on text are timing-dependent accessibility failures.** An audit (or a user) can catch a fading paragraph mid-animation at 3:1 contrast and fail it — Lighthouse did exactly that to us. Text either masks in (clip/transform, full contrast whenever visible) or simply stands. Reserve opacity animation for non-text.
- **A broken accessibility tree now costs you twice.** `aria-label` is prohibited on `<p>` and other generic roles — SplitText's auto-aria adds it, so pass `aria: 'none'` on line splits. The same violation that dings Accessibility also fails Lighthouse's new Agentic Browsing check (`agent-accessibility-tree`) — AI agents navigate by that tree.
- **The render-blocking chain is the whole mobile game.** Inline the CSS into the HTML (a full design system gzips to ~5KB), `defer` every script, and the critical path becomes just the document. Our mobile FCP went 2.3s → 1.7s and TBT 150ms → 20ms from this alone.
- **A preloader must be opt-in per visit, decided before first paint.** Inline head script checks: first visit + no reduced-motion + connection not 2g/3g/saveData → only then show the gate. And if your own deferred JS arrives later than ~1.5s, bail — covering already-painted content is backwards. On every non-loader path, never hide the headline: LCP = FCP.
- **Font swap can move nothing.** Define a local fallback (`src: local('Georgia')`) with fontTools-measured `size-adjust` / `ascent-override` / `descent-override` matched to your webfont; the swap becomes invisible and CLS stays 0.000 even on slow connections.
- **Check inherited caching.** A parent `.htaccess` was silently caching our HTML for a year — updates would never reach returning visitors. Every deployed subfolder gets its own: HTML `no-cache, must-revalidate`, hashed/static assets `max-age=31536000, immutable`, deflate on.
- **Budget the brand beat.** Desktop 100 across five categories is achievable with all of the above; on mobile, one deliberate ~1s first-visit entrance costs roughly 4-6 performance points. Spend them knowingly or not at all — and give every other path (repeat, slow, reduced-motion) the instant version.

## Ship gate

Before calling it done: does the page hold up with every animation off? Does a fast scroller ever see blank? Does Tab reach everything with designed focus? Does a hard-refresh on throttled 3G stay under budget? Does the signature moment survive the question "if I removed this, would the story be weaker — or just quieter?" Then, ideally: make the page measure itself (PerformanceObserver for LCP/CLS/event timing is ~40 lines) — numbers you show live are numbers you can't fake.
