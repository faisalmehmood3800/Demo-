# Agency workspace

## How to work (applies to every task)

The user does not name skills. Choose and load them yourself, every time:

1. Before starting any task, match it against the installed skills, plugins and agents listed in this file and in the session's skill list, and load every one that applies, without waiting to be asked. A task that spans areas loads each area's skills (for example a client launch: sales research, website, n8n chatbot and promo video).
2. Follow each loaded skill's workflow, references and checklists in full; don't skim them or skip their verification steps.
3. Work to the standard of a senior specialist in that field: research before answering, verify facts and results (build, render, test, check at phone width), and deliver finished, client-ready work rather than drafts or placeholders. Never invent facts, data, testimonials or claims; say plainly what is unverified or blocked.
4. When no installed skill covers a task, still work to that standard and say which skill or tool would help, if one exists.
5. Briefly tell the user which skills you used, so they can see why the work looks the way it does.

Communication: reply in the user's language (Roman Urdu when they write in it), research before answering, be honest about limits and risks, and stay encouraging; never demotivate.

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

## Shopify stores (always use the Shopify skills)

For any task that builds, sets up or customises a Shopify store or theme, always use these together:

- `shopify-ai-toolkit` plugin (Shopify's official, from `claude-plugins-official`, enabled in `.claude/settings.json`): its `shopify` skill is the reference for every Shopify API, Liquid, CLI and theme task.
- `shopify-liquid` (lunaticfluker/shopify-liquid-skill, MIT): Online Store 2.0 themes, section schemas, template JSON, Shopify CLI.
- ramonguilhe/claude-skills (no upstream license, personal use only): `shopify-theme-customize`, `shopify-liquid-reference`, `shopify-store-setup`, `shopify-admin-api`, `shopify-storefront-api`, `shopify-app-dev`, `shopify-niche-research`, `shopify-product-research`.

Start every store from a free, Shopify-made Online Store 2.0 theme and customise it; never write a theme from scratch:

- Default: Horizon (github.com/Shopify/horizon), Shopify's newest theme architecture with nested theme blocks, or Dawn (github.com/Shopify/dawn), the all-purpose reference theme.
- Pick another free Shopify theme when it fits the niche better (Theme Store has 24, all by Shopify): Savor, Crave or Taste for food and drink; Studio for artists; Publisher for editorial or independent brands; Refresh or Sense for detailed product pages; Origin for makers; Spotlight for a fast simple launch.
- Customise through sections, blocks and theme settings first, then Liquid edits, and keep changes in a way that survives theme updates. Develop with Shopify CLI (`shopify theme dev`, `theme push`) against the client's store.
- The UI/UX Pro Max and unslop rules above also apply to the store's look.

Shopify's plugin sends usage data, including the prompt, to shopify.dev when its skill runs; it is turned off with `OPT_OUT_INSTRUMENTATION=true` in `.claude/settings.json`. Keep it off unless the user asks otherwise.

## Video

- HyperFrames (heygen-com/hyperframes, Apache 2.0; `hyperframes` plugin enabled in `.claude/settings.json`): the default for videos made in code, such as launch and promo films, explainers, reels and vertical clips, captions on footage, data and chart videos, slideshows and remakes of an existing video. Start with its `hyperframes` skill, then `hyperframes-cli`, `hyperframes-registry` and `media-use` as needed. Render with `npx hyperframes render` and check the result before delivery.
- `ultimate-video-editor`: cutting, grading, audio and export of existing footage with FFmpeg.
- `motion-video-director`: an alternative motion-graphics workflow when the user asks for it.

HyperFrames telemetry is off with `HYPERFRAMES_NO_TELEMETRY=1` in `.claude/settings.json`, so skip the skill's `hyperframes feedback` reports. Keep it off unless the user asks otherwise. Don't sign in to HeyGen or use paid HeyGen media without the user's go-ahead.

## Other installed skills

- Sales: `sales` and `sales-*` skills plus `.claude/agents/sales-*` (zubair-trabzada/ai-sales-team-claude).
- Trading: `trader-*` skills (Jakeschincariol/day-trader-skill).
- n8n: `n8n-*-official` (n8n-io/skills) and the community `n8n-*` skills (czlonkowski/n8n-skills).
- Shopify: `shopify-*` skills and the `shopify-ai-toolkit` plugin (see above).
- Design: `unslop` (heydamir/unslop, CC BY 4.0).
- Video: `hyperframes` plugin (heygen-com/hyperframes, Apache 2.0), `ultimate-video-editor` (Rajbharti06/Ultimate-Video-Editing-Skills) and `motion-video-director` (mattypark/Motion-graphics-skill; upstream has no license, personal use only).

## Motion

- Motion explains something: at most one entrance on load, one easing curve, no looping, parallax or hover-scale effects (see unslop pass 9).
- Respect `prefers-reduced-motion` everywhere.
- Animate `transform` and `opacity`, not layout properties.
- Test every site at phone width (about 390px) before delivery.
