# Agency workspace

## UI, UX and website work

For any task that designs, builds, reviews or fixes a user interface (websites, landing pages, components, colors, typography, layout, animation, accessibility), always load the `ui-ux-pro-max` skill first and follow its workflow:

1. For a new site or page, generate a design system with `--design-system` (add `--motion <1-10>` for animation guidance) and persist it in the project with `--persist --output-dir <project-root>`.
2. Use focused `--domain` and `--stack` searches (`nextjs`, `shadcn`, `html-tailwind`) for specific concerns.
3. Before delivering, read `.claude/skills/ui-ux-pro-max/references/pro-rules.md` and `references/quick-reference.md`, and run through the pre-delivery checklist.

Treat the skill's results as recommendations: adapt fonts, palette and style to the client's actual business rather than copying generic output.

## Component sources

- 21st.dev: install only with the exact command copied from the component's own page, `npx shadcn@latest add "https://21st.dev/r/<author>/<component>"`. Never guess a component link.
- Magic UI: `npx shadcn@latest add "https://magicui.design/r/<component>"`.
- Aceternity UI and React Bits: copy the component code from the site.
- LottieFiles: download the animation file and play it with the installed Lottie libraries. Check each animation's license before use on a client site.

## Motion

- Respect `prefers-reduced-motion` everywhere.
- Animate `transform` and `opacity`, not layout properties.
- Test every site at phone width (about 390px) before delivery.
