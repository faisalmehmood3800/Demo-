---
name: pro-website-playbook
description: The agency's playbook for premium, client-ready websites, distilled from four tutorials the user chose (3D/scroll-animated hero sites, Apple-style scroll storytelling, fast local-business sites, and a services site with admin and lead pipeline). Use on every website, landing page or online store build, alongside ui-ux-pro-max and unslop: it covers reference research, the design brief, AI-generated hero assets with no baked-in text, scroll-synced video or frame-sequence heroes, product imagery, e-commerce checkout and Pakistani payment gateways, backend for leads, and how to sell the result.
---

# Pro website playbook

Sources the user asked me to learn from (Hindi auto-transcripts, read in full on 2026-11-27):

- "How I Build & Sell $4,000 3D Animated Websites without Claude", youtube.com/watch?v=W4dtBxE0Vvw
- "How to Build a Website for Local Businesses in 14 minutes", youtube.com/watch?v=AXMyFEBUteM
- "How I Vibe coded Apple-Style $5000 Website With AI (Claude)", youtube.com/watch?v=YvH8l_3r_GA (read 2026-11-27)
- "CLAUDE CODE FULL COURSE - Build & Sell AI Apps (2026)", youtube.com/watch?v=_FZcKRS74P4 (read 2026-11-27)
- "Claude Opus 4.7 + Claude New Design taking Every Designer job", youtube.com/watch?v=mhxSWQSL3dY (read 2026-11-27)

Detailed notes from the later tutorials, including automation and selling, are in `references/tutorial-notes-2026-11-27.md` (sections A to E) and `references/tutorial-notes-batch2-2026-11-27.md` (sections F to M, including step-by-step notes for the 3D site and local-business videos above). Automation recipes live in `n8n-agency-playbook`; offers and pricing in `agency-offer-playbook`.

Both videos spend much of their time on swapping Claude Code to cheaper models (MiniMax) or a wrapper (Pilotdeck). That part does not apply here. What follows is the craft that does. The `$2,000 to $4,000` price is the presenter's claim, not a verified market rate.

This playbook works inside the rules of CLAUDE.md: ui-ux-pro-max sets the design system, unslop decides how it looks, and the component sources supply the parts. Where this file and unslop disagree on looks, unslop wins.

## 1. Research references before any code

1. Find 3 to 6 references for the client's niche: Dribbble and Pinterest for design, real competitor sites in the target market (for example "luxury watches USA", "biryani restaurant Lahore"), and motion libraries such as motionsites.ai for hero ideas.
2. From each reference take only the style: background colour, type families and sizes, how type is placed, palette, shadows, card treatment, spacing. Strip everything else (their copy, images, canvas, cursor effects, tracking). Never copy a premium or proprietary design whole; take qualities, not layouts.
3. Record the result in the unslop direction (product, character, world, moments, signature) and the ui-ux-pro-max design system.

## 2. Brief first, then build

- Set up the brand's design system before the first page, deck or carousel: name, logo, fonts, colours, assets, notes. Every later piece then stays consistent (the Claude Design tutorial's main advice; here that means the ui-ux-pro-max design system persisted in the project, and for decks, carousels or visual designs the Artifact tool's quickstart with a design system).
- For client sign-off, agree the layout first as a wireframe (boxes: where the headline, button and image go), then show the high-fidelity version. Clients change their minds on layout cheaply at the wireframe stage.

- Collect the full brief before building: brand name, offer, audience, pages, real facts (phone, address, hours, prices), assets, colours and fonts from step 1. One complete brief up front beats rebuilding later; the presenter had to rebuild because he gave the design late.
- If anything is missing, ask the questions first (brand name, colours, stack) instead of guessing. Plan, get the plan approved, then build.
- Build as one component per section (navbar, hero, product grid, footer) with media in `public/`, so later changes are local edits.

## 3. The hero: one rich moment

The premium look comes from one hero moment, usually a product or dish that animates as the visitor scrolls. Everything around it stays quiet (unslop).

Asset pipeline:

1. Start from a real product photo (the client's, ideally) or a sourced reference.
2. Generate the hero still with an image model (Google Flow / Nano Banana, Gemini or ChatGPT images). Compose the product into the reference's look, at 16:9 for desktop and 9:16 for mobile.
3. The hero image and video must contain no text. Headlines, buttons and prices are HTML layered on top, so they stay sharp, editable and translatable. Baked-in text was the tutorial's biggest visible mistake.
4. Match the asset's background to the site's background (pure black or the exact neutral) so the hero merges into the page instead of sitting in a box.
5. Generate several variations and pick by testing; first results are rarely right. When a model will not swap the product, give it a clean screenshot or PNG of the product rather than a WebP.
6. Animate it with image-to-video: give a start frame (the hero still) and, if the tool allows, an end frame, and describe the motion ("the parts separate in an exploded view, then reassemble"; "the lid opens and the movement is revealed"). Keep the camera and background still so only the object moves.

This environment has no image or video generation tool. Either the user generates the assets from the prompts I write (free tiers of Google Flow or Gemini work from a phone), or the client supplies real footage, or, with the user's go-ahead, paid HeyGen media through the HyperFrames `media-use` skill. Never ship a labelled placeholder.

Prompt templates I give the user (fill the brackets from the brief):

- Still: "Professional studio product photograph of [product], [material and colour details], centred, on a seamless [exact background colour] background, soft key light from upper left, subtle reflection, empty space on the [left/right] for a headline, no text, no logo, no watermark, [16:9 or 9:16]."
- Video: "Using this image as the first frame, keep the camera and the [background colour] background completely still. [Product] slowly [separates into its parts in an exploded view / opens / rotates 30 degrees], then returns to its starting position. Smooth, realistic, studio lighting, no text, 5 to 8 seconds."

## 4. Scroll-synced hero: how to build it

Two techniques, both scrubbed by GSAP ScrollTrigger (installed in `website/`):

- Video scrub: an MP4 hero whose `currentTime` follows scroll. Simple, but seeking is jerky on many phones. Encode with frequent keyframes (`ffmpeg -i in.mp4 -an -c:v libx264 -g 1 -crf 23 -pix_fmt yuv420p out.mp4`).
- Frame sequence (smoother, the "flip-book" from the docmo.agency reel): extract frames and draw them on a canvas.
  - `ffmpeg -i hero.mp4 -vf "fps=24,scale=1600:-2" -c:v libwebp -quality 72 frames/%04d.webp`, plus a separate set near 720px wide for phones.
  - Budget: about 120 to 200 frames, under 6 MB total on mobile. Preload the first frames, lazy-load the rest, draw with `requestAnimationFrame`, and pin the hero section for the length of the scrub.
- Always: a static poster frame for `prefers-reduced-motion` and while loading, the headline readable without the animation, and a check at 390px wide on a mid-range phone.
- Apple-style storytelling section: a second pinned section where the product animates with scroll and short text blocks fade and rise in beside it, one idea per step (feature, material, number). Keep it to one such section per page so it stays special, and write each text block from the client's real facts.
- A background hero video must feel embedded: same background colour as the page, no visible frame or letterbox, poster frame first, `muted playsinline`, and compressed (under about 3 MB on mobile).
- Sound (the reel's "sizzle" and scroll sounds) only plays after a user gesture and is off by default with a visible toggle; browsers block autoplay audio and unexpected sound reads as spam.

## 5. Product and listing imagery

- Generate or edit a consistent product set: same background, light and angle, background removed where the layout needs it. Four variations per product, keep the best.
- Product cards: one image style across the grid, real names and prices from the client, no fake reviews or stock counts.

## 6. Commerce and backend

- Card payments in Pakistan: Stripe does not list Pakistan as a supported country. Use PayFast, Safepay, XPay or PayPro (all offer hosted checkout or payment links), JazzCash or Easypaisa wallets, or Shopify with these gateways, plus cash on delivery for local stores. Stripe is only for a client whose business is registered in a Stripe country.
- Always build and test checkout in the gateway's sandbox or test mode first, then switch to live keys from the client's own account. Keys go in environment variables, never in code.
- For the agency's own services site (or a client's), a private `/admin` area can turn the site into a sales tool: login behind a strong password or OAuth, an ideal-customer-profile form, a leads table with stages (new, contacted, replied, meeting, client, not interested), notes and CSV export, backed by Supabase tables. Lead-data APIs such as Apollo need a paid plan for people search; respect each source's terms and data-protection rules.
- All keys go in `.env.local` (never committed, never in client-side code), with an `.env.example` listing the names.
- Forms, sign-ups and lead magnets (for example a free guide download) need storage: Postgres (or a hosted service such as Supabase) behind a small Node.js API, or an n8n webhook into a sheet or CRM (use the n8n skills). Every form gets validation, spam protection and a confirmation.

## 7. Check like a client

- Run it locally and look at it in a real browser after every major step; screenshot desktop and 390px mobile.
- Screenshot loop: after each section is built, take a screenshot (Playwright is installed here), compare it with the brief and references, and fix what is off before moving on. Then check every breakpoint: 390 phone, 768 tablet, 1280 laptop, 1440+ desktop.
- When debugging data or API calls, read the browser's Network tab (request, response, status) rather than guessing.
- Fix what reads as AI-made: baked-in text, mismatched backgrounds, generic fonts, white boxes around assets (run the unslop audit).
- When the model cannot get a detail right, edit the component directly; the code is plain and per-section.

## 8. Selling it

From the tutorial: three channels all still work: content (show the build), personalised LinkedIn outreach, and Upwork or Fiverr with tailored proposals; generic mass outreach no longer does. Use the sales skills for research, outreach and proposals. Demo projects are always labelled as demos or concepts, and client work is shown only with the client's permission.
