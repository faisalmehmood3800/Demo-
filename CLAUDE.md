# Agency workspace

## UI, UX and website work

For any task that designs, builds, reviews or fixes a user interface (websites, landing pages, components, colors, typography, layout, animation, accessibility), always load the `ui-ux-pro-max` skill first and follow its workflow:

1. For a new site or page, generate a design system with `--design-system` (add `--motion <1-10>` for animation guidance) and persist it in the project with `--persist --output-dir <project-root>`.
2. Use focused `--domain` and `--stack` searches (`nextjs`, `shadcn`, `html-tailwind`) for specific concerns.
3. Before delivering, read `.claude/skills/ui-ux-pro-max/references/pro-rules.md` and `references/quick-reference.md`, and run through the pre-delivery checklist.

Treat the skill's results as recommendations: adapt fonts, palette and style to the client's actual business rather than copying generic output.

## Unslop (every website, so nothing looks AI-made)

Every website or UI build also uses the `unslop` skill (heydamir/unslop, CC BY 4.0, keep its credit):

1. Before designing, write the unslop direction (product, character, world, moments, signature) from the client's real business, and use it alongside the ui-ux-pro-max design system.
2. Build to its rules from the start (`references/tokens.md`, `references/signs.md`), not only as a cleanup at the end.
3. Before delivery, run unslop's audit against `references/signs.md` and its checklist, fix what it finds, and pass the template check: if another business's name fits the page just as well, push the direction further.

Where the rules conflict, unslop wins on how the page looks: no hue gradients, gradient text, glows, colored blobs, emoji icons, "New ✨" badges, uppercase tracked labels, looping or bouncy motion, or hype copy, even when a palette, style or component suggests them. Components from the sources below are used only when they fit the direction; drop or restyle any that add those tells (for example glow, shimmer, sparkle or meteor effects).

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

## Automations and AI agents (always use the n8n skills)

For any task that builds, edits, reviews or debugs an automation, workflow, chatbot or AI agent, build it in n8n and always use both installed n8n skill sets:

- Official skills from n8n-io/skills: the `n8n-*-official` skills, routed by `using-n8n-skills-official`.
- Community skills from czlonkowski/n8n-skills: the `n8n-*` skills without the `-official` suffix, routed by `using-n8n-mcp-skills`.

Workflow:

1. Load `using-n8n-skills-official` and `using-n8n-mcp-skills` first, then the specific skills the task needs (for example `n8n-agents-official` and `n8n-agents` for an AI agent, `n8n-workflow-patterns` for structure, `n8n-error-handling-official` for webhooks and unattended runs).
2. Where an official and a community skill cover the same topic, read both and follow the official one if they disagree.
3. Validate the workflow (`n8n-validation-expert`) and add error handling before delivery.
4. If no n8n MCP server is connected, deliver the workflow as an importable JSON file and say so; with one connected, build and test it in the user's n8n directly.

## Other installed skills

- Sales: `sales` and `sales-*` skills plus `.claude/agents/sales-*` (zubair-trabzada/ai-sales-team-claude).
- Trading: `trader-*` skills (Jakeschincariol/day-trader-skill).
- n8n: `n8n-*-official` (n8n-io/skills) and the community `n8n-*` skills (czlonkowski/n8n-skills).
- Design: `unslop` (heydamir/unslop, CC BY 4.0).
- Video: `ultimate-video-editor` (Rajbharti06/Ultimate-Video-Editing-Skills) and `motion-video-director` (mattypark/Motion-graphics-skill; upstream has no license, personal use only).

## Motion

- Motion explains something: at most one entrance on load, one easing curve, no looping, parallax or hover-scale effects (see unslop pass 9).
- Respect `prefers-reduced-motion` everywhere.
- Animate `transform` and `opacity`, not layout properties.
- Test every site at phone width (about 390px) before delivery.
