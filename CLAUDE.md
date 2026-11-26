# Agency workspace

## UI, UX and website work

For any task that designs, builds, reviews or fixes a user interface (websites, landing pages, components, colors, typography, layout, animation, accessibility), always load the `ui-ux-pro-max` skill first and follow its workflow:

1. For a new site or page, generate a design system with `--design-system` (add `--motion <1-10>` for animation guidance) and persist it in the project with `--persist --output-dir <project-root>`.
2. Use focused `--domain` and `--stack` searches (`nextjs`, `shadcn`, `html-tailwind`) for specific concerns.
3. Before delivering, read `.claude/skills/ui-ux-pro-max/references/pro-rules.md` and `references/quick-reference.md`, and run through the pre-delivery checklist.

Treat the skill's results as recommendations: adapt fonts, palette and style to the client's actual business rather than copying generic output.

## Component sources (use these on every website)

Every website build uses the sources below for components, templates and animation, instead of hand-writing what they already provide. Pick what fits the client; don't pile on effects.

Default stack: Next.js + Tailwind + shadcn/ui (see `website/`, where shadcn is initialized and `components.json` registers the registries below). For a new site, start from this setup.

- shadcn/ui (ui.shadcn.com): base components, `npx shadcn@latest add <component>`.
- Magic UI (magicui.design): `npx shadcn@latest add @magicui/<component>`.
- Aceternity UI (ui.aceternity.com): `npx shadcn@latest add @aceternity/<component>`.
- React Bits (reactbits.dev): `npx shadcn@latest add @reactbits/<Component>-TS-TW` (name as shown on the component's page, e.g. `SplitText-TS-TW`).
- 21st.dev: install only with the exact command copied from the component's own page, `npx shadcn@latest add "https://21st.dev/r/<author>/<component>"`. Never guess a component link.
- LottieFiles (lottiefiles.com): download the animation file and play it with `@lottiefiles/dotlottie-react` or `lottie-web`. Check each animation's license before use on a client site.
- Motion (motion.dev): the `motion` package, for React UI animation, gestures, layout and scroll effects.
- GSAP (gsap.com): the `gsap` package, for timelines, ScrollTrigger and complex sequences.

Use `--dry-run` or `--view` first to see what a component adds, and review third-party component code before shipping it.

## Other installed skills

- Sales: `sales` and `sales-*` skills plus `.claude/agents/sales-*` (zubair-trabzada/ai-sales-team-claude).
- Trading: `trader-*` skills (Jakeschincariol/day-trader-skill).
- n8n: `n8n-*-official` (n8n-io/skills) and the community `n8n-*` skills (czlonkowski/n8n-skills).
- Video: `ultimate-video-editor` (Rajbharti06/Ultimate-Video-Editing-Skills) and `motion-video-director` (mattypark/Motion-graphics-skill; upstream has no license, personal use only).

## Motion

- Respect `prefers-reduced-motion` everywhere.
- Animate `transform` and `opacity`, not layout properties.
- Test every site at phone width (about 390px) before delivery.
