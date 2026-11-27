# Product reviews on WhatsApp after delivery (Shopify): setup guide

**Roman Urdu mein khulasa:** jab order customer tak deliver ho jata hai, us ke 3 se 4 din baad customer ko WhatsApp par message jata hai ke product kaisa laga, saath mein 5 buttons (5 se 1 tak rating). Customer rating dabata hai, phir bot 1-2 line ka review maangta hai, aur sab kuch ek table mein save ho jata hai. Agar rating 1 ya 2 ho, to store owner ko foran email alert jata hai taake woh customer ko call kar sake. Ye sirf WhatsApp par chalta hai.

This is an add-on for the COD confirmation workflow (`cod-order-confirmation-whatsapp-email.json`). It uses the same WhatsApp number.

## How it works (example)

1. Ali's order #1004 (Running Shoes, Cotton T-shirt) is delivered on Monday.
2. The workflow learns about the delivery in one of two ways:
   - your courier's Shopify app marks the shipment **delivered**, or
   - you (or your team) add the tag **`Delivered`** to the order in Shopify.

   The order is saved once in the `reviewRequests` table as `waiting`, even if Shopify sends the update again.
3. Every evening at 6:13 pm (Pakistan time) the workflow checks for orders delivered at least 3 days ago. Ali's order qualifies on Thursday, so he gets:

   > Assalam o Alaikum Ali, your order #1004 (Running Shoes, Cotton T-shirt) was delivered a few days ago. How did you like it? Please tap a rating below.
   > **[5 - Excellent] [4 - Good] [3 - Average] [2 - Poor] [1 - Very poor]**

4. Ali taps **2 - Poor**. The rating is saved and he gets:
   > Shukriya, aap ki rai hamare liye zaroori hai. Kya aap 1-2 line mein batayenge kya acha laga ya kya behtar ho sakta tha?

   Because it is 1 or 2 stars, the store owner gets an email: *"Low rating (2/5) on order #1004 … WhatsApp: +923001234567. Consider calling them today."*
5. Ali writes: "Shoes ka size thora chota tha, exchange chahiye". It is saved as his review and he gets: *"Shukriya Ali! Aap ka review save ho gaya hai."*
6. If he taps a rating again later, nothing changes. Messages from other customers are ignored by this workflow.

The table then shows: order, products, delivery date, rating `2`, review text and status `reviewed`. You can download it from n8n (Data tables → reviewRequests) and use the good reviews on your site or Instagram with the customer's permission.

## Rules this follows

- **Utility template, no promotion.** Meta allows feedback requests as Utility only when they are about a specific order. Keep the template about the order only: no discount, coupon or "shop again" line, or Meta moves it to the Marketing category (higher price) or rejects it.
- **No review gating.** Every customer gets the same message whatever they rate. If you set a public review link, it is offered to everyone, not only happy customers. Asking only happy customers for public reviews breaks Google's review policy.
- **Free replies.** After the customer taps a button, the 24-hour service window is open, so the follow-up questions and thank-you are service messages (free up to Meta's monthly free tier, per Meta's pricing from 1 October 2026). The first review request is a paid utility message (about USD 0.015 in Pakistan per third-party sources; check Meta's rate card).

## Step 1: WhatsApp template

In WhatsApp Manager create:

- **Name:** `order_review_request`
- **Category:** Utility
- **Language:** English (`en`), the same as your other templates
- **Body** (3 variables):

```
Assalam o Alaikum {{1}}, your order {{2}} ({{3}}) was delivered a few days ago. How did you like it? Please tap a rating below.
```

- **Buttons:** five **Quick reply** buttons in this exact order: `5 - Excellent`, `4 - Good`, `3 - Average`, `2 - Poor`, `1 - Very poor`.

Variables: 1 first name, 2 order number, 3 product names. WhatsApp shows the first buttons and puts the rest under "See all options".

## Step 2: Data Table

In n8n create the Data Table **`reviewRequests`** with these columns, all type **string**:

`orderId, orderName, phone, customerName, items, deliveredAt, status, rating, reviewText, askedAt, waMessageId`

Status values: `waiting` (delivered, not asked yet), `asked`, `rated`, `reviewed`, `send_failed`.

## Step 3: Import and configure

1. Import `review-request-whatsapp.json`.
2. Open **Review settings**:

| Setting | What to put |
|---|---|
| `store_name` | Your store name |
| `review_template_name` / `template_language` | `order_review_request` / `en` |
| `whatsapp_phone_number_id` | Same as in the COD workflow |
| `default_country_code` | `92` |
| `review_after_days` | `3` (the daily run then sends 3 to 4 days after delivery) |
| `delivered_tag` | `Delivered`, the tag you add in Shopify when a parcel is delivered |
| `max_per_day` | Most review requests sent per day (default 100) |
| `public_review_link` | Optional: link to your product reviews page or Google review link; leave empty to skip |
| `owner_email` | Where low-rating alerts go |
| `email_from` | Same sender as in the COD workflow |

3. Credentials:

| Node | Credential |
|---|---|
| Delivery update (courier), Order updated (Delivered tag), Get order from Shopify | Shopify Access Token API (needs `read_orders`, `read_fulfillments`) |
| Ask for a review, Ask for a few words, Thank the customer | WhatsApp API |
| Alert the owner | SMTP |

4. **Publish this workflow first.**
5. Open the **COD workflow** → node **Send to review workflow** → choose *Product Reviews on WhatsApp after Delivery* → right-click → **Activate** (it ships deactivated so the COD workflow works without reviews) → publish the COD workflow.

Why step 5: a Meta app sends all WhatsApp messages to one webhook, so only one workflow can have the WhatsApp Trigger. The COD workflow receives everything, keeps the Confirm/Cancel taps, and passes every other customer message here. n8n refuses to publish the COD workflow while it points at an unpublished workflow, so the order matters.

## How delivery reaches Shopify in Pakistan

- **Courier app:** some couriers' Shopify apps update the shipment status to *delivered*. Check yours: open a delivered order in Shopify and see whether it shows *Delivered* in the fulfilment timeline.
- **If not:** add the tag `Delivered` to delivered orders (you can select many orders in the Shopify order list and add the tag in one go, for example when the courier sends its delivered list or cash payment report).

## How it was tested

Run on a local n8n 2.49 together with the COD workflow, with a real Data Table and real sub-workflow calls. Shopify and WhatsApp calls and the owner email were replaced by stand-ins that record what would be sent:

| # | Test | Result |
|---|---|---|
| 1 | Courier marks order 8001 delivered | Order fetched, row `waiting` with phone `923001234567` and products `Running Shoes, Cotton T-shirt` (duplicates removed) |
| 2 | Same delivery event again | Skipped, no duplicate row |
| 3 | "Out for delivery" event | Ignored |
| 4 | Owner adds the `Delivered` tag to order 8002 | Row `waiting` |
| 5 | Order update without the tag | Ignored |
| 6 | Delivered order with no phone | Skipped |
| 7 | Daily run on the same day | Nobody asked (too early) |
| 8 | Daily run for an order delivered 4 days ago | Template `order_review_request` to `923001234567` with `Ali`, `#1004`, products, buttons `RATE5:8004` … `RATE1:8004`; row `asked` |
| 9 | Customer taps 2 - Poor (through the COD workflow) | Row `rated` 2, follow-up question sent, owner alert email "Low rating (2/5) on order #1004" |
| 10 | Customer writes a line | Saved as `reviewText`, row `reviewed`, thank-you sent |
| 11 | Taps a rating again | Ignored |
| 12 | Unrelated message from another number | Ignored |
| 13 | COD workflow regression (all 9 COD tests) | All passed with the new routing |

Not covered: the live Shopify webhooks, your courier's delivery updates, Meta's template approval and real delivery. Test with one of your own orders: tag it `Delivered`, set `review_after_days` to `0` for the test, wait for the 6:13 pm run (or press *Execute* on **Every day 6:13 pm**), then set it back to `3`.

## Troubleshooting

| Problem | Fix |
|---|---|
| Delivered orders don't appear in the table | Your courier app may not mark delivery: use the `Delivered` tag. The Shopify token needs `read_orders` and `read_fulfillments`. |
| No review message | Template approved and named exactly as in Review settings? Rows with `send_failed` show who could not be reached. |
| Rating tap does nothing | Is **Send to review workflow** in the COD workflow activated, pointing at this workflow, and are both workflows published? |
| Template moved to Marketing | Remove any promotional words and keep it about the specific order. |

## Licence

Built by [your agency name]. You may use and adapt this workflow for your own store. Reselling the workflow file itself is not allowed.
