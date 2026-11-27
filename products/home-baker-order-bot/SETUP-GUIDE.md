# Home Bakery WhatsApp Order Assistant: setup guide

**Roman Urdu mein khulasa:** ye n8n workflow aap ke WhatsApp Business number par customers se baat karta hai: menu aur qeemat batata hai, order ki tafseel leta hai, total ke saath order confirm karwata hai aur Google Sheet mein save kar deta hai. Aap Sheet se orders dekh kar khud confirm karte hain. Setup mein taqreeban 1 se 2 ghante lagte hain.

## What it does

- Answers customers on WhatsApp in Roman Urdu, Urdu or English.
- Reads your menu and prices from a Google Sheet, so you update prices in the sheet, not in the bot.
- Collects name, items, size, date and time, pickup or delivery, address and cake message.
- Repeats the full order with the total and saves it only after the customer says yes.
- Saves every confirmed order as a new row in your **Orders** sheet with status `New`.
- Replies politely to voice notes and pictures asking the customer to type.
- If the AI fails, the customer gets a polite "we'll reply shortly" message instead of silence.
- Hands complaints, custom designs and anything unclear back to you.

It does not take payments, change prices or confirm stock on its own. You stay in control.

## What you need

| Item | Notes |
|---|---|
| n8n | n8n Cloud or a self-hosted n8n (version 2.x; built and tested on 2.49) reachable over HTTPS |
| WhatsApp Business Platform (Cloud API) | A Meta developer app with the WhatsApp product, a business phone number, an access token and the business account ID |
| OpenAI API key | With a little balance. The workflow uses `gpt-4.1-mini`; you can pick another model in the node |
| Google account | For the Google Sheet |

### WhatsApp costs (check before you start)

Meta charges per delivered message on the Business Platform. When a customer messages you, a 24-hour customer service window opens and the bot's replies inside it are service messages. According to Meta's pricing page, from 1 October 2026 each business phone number gets a monthly free tier of service messages, and replies beyond it are charged. Third-party sources put the free tier at 1,000 replies per number per month and the charge in Pakistan at about USD 0.015 per message after that. Check Meta's current rate card for your country. This bot only replies to customers who message first, so it never sends paid marketing templates.

Also check in Meta's setup whether the number you want can stay on the WhatsApp Business app at the same time (coexistence), or whether you need a separate number for the Cloud API.

## Step 1: Google Sheet

1. Create a Google Sheet named **Home Bakery Orders**.
2. Add a tab named exactly **Menu**. Import `menu-template.csv` and replace the sample rows with your real menu. Keep the headers: `item, category, size, price_pkr, available, notes`.
3. Add a tab named exactly **Orders**. Import `orders-template.csv` (headers only): `order_id, received_at, customer_name, whatsapp_number, items, total_pkr, needed_on, pickup_or_delivery, address, notes, status`.
4. Tip: in Google Sheets, turn on *Tools → Notification settings → Notify me when changes are made* so you get an email for each new order.

## Step 2: Import the workflow

In n8n: **Create workflow → ⋯ → Import from file** and choose `home-bakery-whatsapp-order-assistant.json`.

## Step 3: Your bakery details

Open **Bakery settings** and change every value: bakery name, owner name, city, opening hours, order notice, delivery areas and charge, payment methods. The assistant only uses these facts and your menu, so keep them accurate.

## Step 4: Credentials

| Node | Credential |
|---|---|
| WhatsApp Trigger | *WhatsApp OAuth* (Meta app ID and app secret) |
| Send reply, Send sorry message, Ask to type | *WhatsApp API* (permanent access token and WhatsApp business account ID) |
| OpenAI model | *OpenAI* API key |
| Get_menu, Save_order | *Google Sheets OAuth2* |

Then in **Get_menu** and **Save_order**, pick the **Home Bakery Orders** document. The tabs are already set to `Menu` and `Orders`.

Use a permanent (system user) access token for WhatsApp; temporary tokens from the Meta dashboard expire in 24 hours.

## Step 5: Test, then go live

1. In Meta's WhatsApp setup, add your own phone number as a test recipient if your app is still in development mode.
2. **Publish** the workflow. The WhatsApp Trigger registers the webhook with Meta when it is active.
3. From your phone, message the business number: "Salam, chocolate cake kitne ka hai?" and then place a test order.
4. Check **Executions** in n8n and the **Orders** tab. Delete your test rows.
5. Try a voice note: the bot should ask you to type.

## Customising

- **Tone and rules:** edit the system message in **Order assistant** (for example, add your cake decorating rules or minimum order).
- **Memory:** the bot remembers the last 12 messages per customer number. The memory is stored inside n8n and is lost on restart; for multi-server (queue mode) setups use a Postgres or Redis memory node instead.
- **Model:** any chat model that supports tool calling works.

## Troubleshooting

| Problem | Fix |
|---|---|
| No reply at all | Is the workflow published? Is your number allowed as a test recipient? Check **Executions**. |
| "Authorization failed" on a WhatsApp node | The access token expired. Create a permanent system user token. |
| OpenAI error | Key wrong or no balance. The customer receives the sorry message meanwhile. |
| Menu answers are wrong | The bot reads the Menu tab. Check the tab name is exactly `Menu` and prices are in `price_pkr`. |
| Order not in the sheet | Tab must be named exactly `Orders` with the headers above. The bot saves only after the customer confirms. |
| Bot replies to itself | It shouldn't: status updates are filtered by **Is it a customer message?**. Don't remove that node. |

## Privacy

Customer names, numbers and addresses are stored in your Google Sheet and sent to OpenAI to write replies. Tell customers you use an automated assistant, keep the sheet private, and delete old orders you no longer need.

## Support and licence

Built by [your agency name]. You may use and adapt this workflow for your own business. Reselling the workflow file itself is not allowed.
