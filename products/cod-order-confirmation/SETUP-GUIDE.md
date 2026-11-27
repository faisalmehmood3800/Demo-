# COD Order Confirmation on WhatsApp + Email (Shopify): setup guide

**Roman Urdu mein khulasa:** jab bhi Shopify store par Cash on Delivery order aata hai, customer ko **WhatsApp aur email dono** par order ki tafseel ke saath Confirm aur Cancel ka option jata hai. Customer jis bhi channel par pehle jawab de, wahi faisla lagta hai: Shopify order par tag lag jata hai (COD Confirmed ya COD Cancelled), aur doosre channel par customer ko khud update chala jata hai. Customer kisi bhi ek jagah se confirm kare, **WhatsApp aur email dono par** "order confirmed" ka message chala jata hai, aur doosri jagah button dabane ki zaroorat nahi rehti. Cancel bhi isi tarah hota hai. Is se jhooti COD orders aur wapas aane wale parcels kam hote hain.

## What it does

1. A new Shopify order arrives (t-shirts, shoes, anything).
2. If it is a Cash on Delivery order, the customer gets:
   - **WhatsApp:** your approved template with order number, items, total and two buttons, Confirm and Cancel (if the order has a phone number).
   - **Email:** the same details with Confirm order and Cancel order buttons (if the order has an email address).
3. The order is saved in an n8n Data Table as `pending`, with a secret 40-character token for its email links and whether each message was `sent` or `failed`.
4. **One answer wins.** Whichever channel the customer uses first:
   - tags the Shopify order `COD Confirmed` or `COD Cancelled`,
   - saves the status and where it came from (`decidedVia`: `whatsapp` or `email`),
   - answered on WhatsApp: a thank-you or cancellation message on WhatsApp, **plus an automatic status email** ("Order confirmed, the email buttons are no longer needed"),
   - answered by email: a "Thank you, order confirmed" (or "Order cancelled") page in the browser, **plus the same confirmation email**, **plus an automatic WhatsApp update** ("Aap ka order confirm ho gaya hai, buttons dabane ki zaroorat nahi") sent with the `order_status_update` template.
5. **The other channel can't undo it.** If the customer still taps the WhatsApp button, they are told "already confirmed via email" (or cancelled). If they later open the email link, the page says "Order is already cancelled. We received your answer via WhatsApp." Nothing changes twice.
6. Prepaid (card) orders and orders with neither a phone nor an email are skipped.

The workflow never cancels orders, refunds money or changes stock. It only tags orders; you decide in Shopify.

Pakistani numbers are normalised automatically: `0300-1234567`, `03001234567`, `3001234567` and `+92 300 1234567` all become `923001234567`.

### Safety built in

- **Email links are two-step.** Opening the link only shows a page with the order and a "Yes, confirm this order" button. The order changes when that button is pressed. Email security scanners (Outlook, Gmail, company filters) open links automatically, so a one-click link would confirm orders nobody chose.
- **Every email link carries a secret token** for that order. A guessed or edited link gets a "This link is not valid" page (HTTP 400) and changes nothing.
- **WhatsApp taps must come from the order's own number.** A tap carrying another order's ID from a different number is ignored.
- WhatsApp and email sends retry 3 times; a failed send is recorded (`whatsappSent` / `emailSent` = `failed`) and does not stop the other channel.

## What you need

| Item | Notes |
|---|---|
| n8n 2.x | n8n Cloud or self-hosted, **reachable over HTTPS from the internet** (the email buttons open your n8n URL), with Data Tables (built and tested on 2.49) |
| Shopify store | A custom app access token with `read_orders` and `write_orders` |
| WhatsApp Business Platform (Cloud API) | Meta app, business phone number, permanent access token, business account ID |
| Email sending (SMTP) | Your domain's mailbox (for example `orders@yourstore.com` from Hostinger, Zoho or Google Workspace) or a sending service such as Brevo. Use a mailbox on your own domain so emails don't land in spam. |

### Costs

- **WhatsApp:** the confirmation request is a business-initiated **Utility template**, charged per delivered message. The `order_status_update` after an email answer is a second utility message, charged the same way. Third-party sources put the Pakistan rate at about USD 0.015 per utility message from 1 October 2026; check Meta's current rate card. The replies after a button tap are inside the 24-hour service window; from 1 October 2026 those are free up to a monthly free tier per number.
- **Email:** free within your mailbox or sending service's daily limit.

## Step 1: Create the WhatsApp template

In WhatsApp Manager create a template:

- **Name:** `order_confirmation`
- **Category:** Utility
- **Language:** English (`en`), or change `template_language` in Store settings
- **Body** (5 variables, in this order):

```
Assalam o Alaikum {{1}}! Thank you for ordering from {{2}}.
Order {{3}}: {{4}}
Total: {{5}} (Cash on Delivery)
Please confirm so we can dispatch your order.
```

- **Buttons:** two **Quick reply** buttons, in this order: `Confirm order` (first) and `Cancel order` (second).

Variables are filled as: 1 customer first name, 2 store name, 3 order number, 4 items, 5 total.

Create a second template for the automatic WhatsApp update after an email answer:

- **Name:** `order_status_update`
- **Category:** Utility, same language
- **Body** (3 variables, no buttons):

```
Assalam o Alaikum {{1}}, your order {{2}} is now {{3}}. We received your answer by email, so there is no need to press the buttons in our earlier WhatsApp message. Thank you!
```

The workflow fills: 1 first name, 2 order number, 3 `confirmed` or `cancelled`. Wait for Meta to approve both templates.

Why a second template: WhatsApp only allows free text to a customer who has messaged you in the last 24 hours (otherwise Meta returns error 131047). A customer who answered by email hasn't, so the update must be an approved template. A message WhatsApp has already delivered can't be edited, so its old buttons stay visible; if pressed, the customer is told the order is already confirmed and nothing changes.

## Step 2: Create the Data Table

In n8n go to **Data tables → Create**, name it exactly `codConfirmations`, and add these columns, all type **string**:

`orderId, orderName, phone, email, customerName, items, total, shopTags, actionToken, status, decidedVia, whatsappSent, emailSent, waMessageId`

(`id`, `createdAt` and `updatedAt` are added automatically.)

## Step 3: Import and configure

1. **Create workflow → ⋯ → Import from file** → `cod-order-confirmation-whatsapp-email.json`.
2. Open **Store settings** (the only node with your details):
   - `store_name`: your store name as customers know it
   - `whatsapp_phone_number_id`: from Meta → WhatsApp → API setup
   - `template_name`, `status_template_name` / `template_language`: as created in Step 1
   - `default_country_code`: `92` for Pakistan
   - `email_from`: for example `Your Store <orders@yourstore.com>` (must be an address your SMTP account may send from)
   - `support_contact`: shown at the bottom of the email, for example `WhatsApp 0300-0000000`
   - `order_action_url`: your n8n address + `/webhook/cod-order-action`, for example `https://n8n.yourstore.com/webhook/cod-order-action`. Copy it from the **Email link opened** node → *Production URL*.
   - `cod_only`: `true` to message only COD orders
3. **Credentials.** The red triangles you see after import are not errors: they mark the 9 nodes that need a login (Send to review workflow is deactivated and shows no mark) that still need your login. They disappear when you connect these:

| Node | Credential |
|---|---|
| Shopify Trigger, Tag order in Shopify | Shopify Access Token API (shop subdomain + access token + app secret) |
| Send WhatsApp request, Reply: already decided, Reply on WhatsApp, WhatsApp: status update | WhatsApp API (permanent token + business account ID) |
| WhatsApp Trigger | WhatsApp OAuth (Meta app ID + app secret) |
| Send email request, Email: status update | SMTP (host, port, user, password) |

   `canvas-before-credentials.png` shows the 9 marks right after import; `workflow-canvas.png` shows the same workflow with credentials connected: no marks, Publish enabled.
4. If a Data Table node shows "Currently no items exist" under its columns, press the reload button next to the columns. It's a display quirk; the mappings are saved.

## Step 4: Test, then go live

1. **Publish** the workflow (the webhooks only work when published).
2. Place a COD test order with your own WhatsApp number and email.
3. You should get the WhatsApp template and the email within seconds.
4. In the email press **Confirm order**, then **Yes, confirm this order**. Check: Shopify order tagged `COD Confirmed`, Data Table row `confirmed` with `decidedVia` = `email`.
5. Within seconds you should get both the WhatsApp update "your order #… is now confirmed" and a confirmation email. If you still tap **Confirm order** on the old message, you get "already confirmed via email".
6. Place a second test order, tap **Cancel order** on WhatsApp. Check the `COD Cancelled` tag and the status email; then open that order's email link: the page should say it is already cancelled.
7. Cancel the test orders in Shopify.

## How it was tested

Run on a local n8n 2.49 with a real Data Table, the real email webhooks and pages, and the real logic. Shopify, WhatsApp and SMTP calls were replaced by stand-ins that record what would be sent (no real accounts):

| # | Test | Result |
|---|---|---|
| 1 | New COD order, phone `0300-1234567` and email `Ali@Example.com` | WhatsApp template to `923001234567` with `CONFIRM:7001` / `CANCEL:7001`; email to `ali@example.com` with both links and a 40-character token; row `pending`, both sends `sent` |
| 2a | Email link opened (GET) | Confirm page shown, row **still pending** (link scanners can't decide) |
| 2b | Button on that page pressed (POST) | Tag `Online, COD Confirmed`, row `confirmed` via `email`, thank-you page, confirmation email to `ali@example.com`, and the WhatsApp update `order_status_update` to `923001234567` with `Ali`, `#1001`, `confirmed` |
| 3 | Same customer then taps Confirm on WhatsApp | "Aap ka order #1001 pehle hi email ke zariye confirm ho chuka hai…", nothing changed |
| 3b | Tap for that order from a different number | Ignored, nothing changed |
| 4 | Second order, Cancel on WhatsApp | Tag `Online, COD Cancelled`, row `cancelled` via `whatsapp`, WhatsApp cancellation text and status email |
| 5 | That order's email link opened, and a forced POST | "Order #1002 is already cancelled. We received your answer via WhatsApp." Row unchanged |
| 6 | Link with a wrong token | "This link is not valid" page, HTTP 400 |
| 7 | Paid card order | Skipped, nothing sent or saved |
| 8 | COD order with email only | Email sent, no WhatsApp attempted, row `pending` |
| 9 | That email-only customer cancels by email | Tag `COD Cancelled`, row `cancelled` via `email`, cancellation email sent, no WhatsApp attempted, page without the WhatsApp line |

Not covered: the live Shopify webhook, Meta's template approval, real WhatsApp and email delivery, and spam placement of your emails. Do Step 4 with your own accounts before turning it on for customers. If a customer answers on both channels within the same second, the first one to save wins; the second may also tag, which is harmless.

## Add-on: product reviews after delivery

`review-request-whatsapp.json` asks customers for a rating and a short review on WhatsApp 3 to 4 days after delivery. It works through this workflow's WhatsApp Trigger: the node **Send to review workflow** passes every WhatsApp message that is not a Confirm/Cancel tap to the review workflow. That node ships **deactivated**, so this workflow publishes and works on its own. To use reviews, follow `REVIEWS-SETUP.md`.

Only buttons whose payload starts with `CONFIRM:` or `CANCEL:` can change an order here, so a rating tap can never confirm or cancel an order.

## Troubleshooting

| Problem | Fix |
|---|---|
| No WhatsApp on a new order | Is it COD with a phone number? Template approved, name and language exactly as in Store settings? Rows with `whatsappSent` = `failed` are customers to call. |
| No email | Check `emailSent` in the table and the **Send email request** execution. Wrong SMTP login, or `email_from` not allowed by your mail provider. |
| Email in spam | Send from your own domain with SPF and DKIM set up at your mail provider. |
| Email button shows "not valid" | `order_action_url` must be your Production URL, and the workflow must be published. |
| Button tap does nothing | WhatsApp Trigger must be active (published); the template's buttons must be Quick reply, Confirm first. |
| Order not tagged | The Shopify token needs `write_orders`. |
| Token expired | Use a permanent system user token, not the 24-hour test token. |

## Privacy

Order details, phone numbers and emails are stored in your n8n Data Table. Only message customers about their own order, and delete old rows you no longer need.

## Licence

Built by [your agency name]. You may use and adapt this workflow for your own store. Reselling the workflow file itself is not allowed.
