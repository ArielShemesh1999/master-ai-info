# MasterAI

> **A Hebrew, right-to-left landing site for a practical AI course — for Hebrew speakers with no technical background who want to finish with tools, automations and bots that actually run.**

**Live:** [masterai-mu.vercel.app](https://masterai-mu.vercel.app)

Three pages carry the funnel: home, a 6-week syllabus (`course.html`), and three pricing tiers (`pricing.html`). No build step, no framework, no backend. The source repo is private.

## Shipping three self-contained HTML files instead of a framework

Each page is one file — markup, `:root` design tokens, component CSS, per-section JS IIFEs, all inline. The home page is ~280 KB of source in one document. No bundler, no CI, no shared partials — a nav change is three edits, not one. External runtime code is Google Fonts, three pinned CDN scripts — `gsap@3.14.1`, its `ScrollTrigger`, and `moveable@0.53.0`, each loaded with an SRI `integrity` hash and `crossorigin` — and the licensed accessibility widget below, which loads from its own gated endpoint without SRI.

## Making the motion layer fail open

Scroll-scrub parallax, a hero aurora, and settle-on-reveal gradient headlines are driven by GSAP. Because animation is what makes content visible, the layer fails **open**: if GSAP never loads (blocked CDN, script error) or the visitor sets `prefers-reduced-motion: reduce`, everything the reveals would have animated is shown immediately. That branch is load-bearing: `prefers-reduced-motion` appears 22 times in the home page.

## Rendering the promo video from the site's own design tokens

The `#watch` section plays a 21-second showreel that is neither stock footage nor a third-party embed. Its scenes are built from the site's own tokens, rendered deterministically by driving a `seek(t)` hook under headless Chrome, then encoded with ffmpeg to mp4 (1.0 MB) + webm (459 KB) + poster (39 KB). It is self-hosted because the CSP allows no player origin; autoplay is muted and in-view only, and under reduced motion only the poster is served.

## Capturing leads without a backend

There is no server to POST to, so the pricing form (name + phone) composes a prefilled `mailto:`. Cohort framing is evergreen — "the next cohort", never a date — so the page cannot go stale between cycles. There are no testimonials: the proof is structural (three tiers, a 14-day refund, up to 12 interest-free payments), because inventing graduate quotes was off the table.

## Setting the security and caching headers at the edge

`vercel.json` sets CSP (`script-src` limited to `'self'`, jsdelivr and the accessibility-widget origin — plus `'unsafe-inline'`, which the inline per-section IIFEs still require — with `object-src 'none'` and `frame-ancestors 'none'`), HSTS with preload, `X-Frame-Options: DENY`, nosniff, COOP/CORP same-origin, and a Permissions-Policy denying camera, microphone and geolocation. Assets cache immutable for a year; `index.html` must-revalidates.

## Swapping the homegrown accessibility panel for a licensed widget

The in-house a11y button and panel were deleted from all three pages in favour of the licensed `website-accessibility` widget — a separate product of mine — loaded cross-origin and domain-locked. Its icon-only launcher is recolored to the brand cyan-blue by patching the widget's **open** shadow DOM after mount, so the change is scoped to this host and other licensed domains are unaffected. The pages keep their own semantics: a real `<main>`, a Hebrew skip link, one `<h1>` each, ARIA on nav and drawer, `lang="he" dir="rtl"` throughout. JSON-LD covers Organization, WebSite and Course with `hasCourseInstance` (`courseWorkload: P6W`) and `offers`.

## Verifying live: console, mobile widths and reduced motion

Changes were checked against the deployed URL, not just the diff: zero console errors live, mobile at 390 px and 768 px with no horizontal overflow, and a reduced-motion run confirming the poster-only path still shows all content. The June 2026 code/perf/a11y/SEO sweep ran as an adversarial multi-agent audit — 25 agents, 33 confirmed findings — under a pixel-identical design constraint. It removed a dead 404 CDN script that had been failing silently in production.
