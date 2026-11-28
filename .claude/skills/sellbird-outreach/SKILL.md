---
name: sellbird-outreach
description: Sellbird's cold-outreach sales knowledge (ethanplusai/sellbird, AGPL-3.0): finding prospects and buying signals, qualifying leads, finding the decision maker, writing personal cold emails and 3-email sequences that don't read as AI, LinkedIn outreach, handling replies and objections, offer ladders, selling to startups, and honest social posts (finished work, customer questions, testimonials with permission). Use whenever the user wants to find clients, write a cold email or follow-up, plan an outreach campaign, reply to a prospect, handle an objection, or draft a social post that wins business, together with the sales-* skills and agency-offer-playbook.
---

# Sellbird outreach knowledge

Source: [ethanplusai/sellbird](https://github.com/ethanplusai/sellbird), commit `8c743f1`, licensed AGPL-3.0-only (see `LICENSE` and `NOTICE` in this folder; keep them with these files). Sellbird is a full Python outreach app; this skill installs only its sales knowledge files, which the app's own agents load before deciding. The app itself (sending from a mailbox, dashboard, MCP tools such as `social_post_draft`) is not installed here and needs Python 3.11, a Claude or ChatGPT plan, and a sending mailbox on the user's own machine.

## Which file to read

| Task | Read |
|---|---|
| Find businesses that need the service, trigger events | `references/prospecting_tactics.md`, `references/signal_playbook.md` |
| Decide if a lead is worth it (BANT, ICP score, disqualify) | `references/lead_qualification.md` |
| Who to contact at a company, multi-threading | `references/account_navigation.md` |
| Write a cold email, subject line, 3-email sequence | `references/email_frameworks.md` (read in full), `references/signal_playbook.md` |
| Conversation flow, tone, ethics | `references/sales_methodology.md` |
| What to offer next, closing, timing | `references/offer_strategy.md` |
| A prospect objects ("too expensive", "not now") | `references/objection_handling.md` |
| LinkedIn connection and message sequence | `references/linkedin_outreach.md` |
| Prospect is a startup, YC or recently funded | `references/selling_to_startups.md` |
| Social post that brings clients | `references/posting_to_social.md`, then the matching `references/social_*.md` playbook |
| Someone mentioned the brand online | `references/brand_mentions.md` |

## Rules that always apply

1. Compliance first: truthful subject lines (no fake "re:"/"fwd:"), real identity, a working opt-out, and any "stop" or "remove me" is a permanent unsubscribe. Check the law of the recipient's country (CAN-SPAM, GDPR/PECR for EU/UK, Australia's Spam Act, PECA/local rules for Pakistan and the Gulf).
2. Never invent a customer, quote, number, result or signal. A social proof post needs the customer's exact words and their permission.
3. Nothing is sent or posted without the user's approval of the exact text.
4. Adapt Sellbird's US-centred examples and prices to the user's market (Pakistan, the Gulf, Singapore, Australia), and pair it with `agency-offer-playbook` for packages and pricing and the `sales-*` skills for research, proposals and reports.
5. The app's tool names (`social_playbooks`, `social_post_draft` and others) exist only when the Sellbird app is installed and connected; without it, draft in chat or in a file for the user to send.
