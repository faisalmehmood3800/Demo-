# COD Order Confirmation on WhatsApp (Shopify): setup guide

**Roman Urdu mein khulasa:** jab bhi aap ke Shopify store par Cash on Delivery order aata hai, customer ko WhatsApp par order ki tafseel ke saath do button jate hain: Confirm aur Cancel. Customer jo button dabata hai, Shopify mein order par khud tag lag jata hai (COD Confirmed ya COD Cancelled) aur customer ko shukriya ka message chala jata hai. Is se jhooti COD orders aur wapas aane wale parcels kam hote hain.

## What it does

1. A new Shopify order arrives (any product: t-shirts, shoes, anything).
2. If it is a Cash on Delivery order with a phone number, the customer gets your approved WhatsApp template with the order number, items and total, plus two buttons: Confirm and Cancel.
3. The order is saved in an n8n Data Table as `pending`. If WhatsApp fails, it is saved as `send_failed` so you can call the customer.
4. When the customer taps a button:
   - **Confirm:** the order gets the tag `COD Confirmed`, the row becomes `confirmed`, and the customer gets a thank-you message.
   - **Cancel:** the order gets the tag `COD Cancelled`, the row becomes `cancelled`, and the customer gets a cancellation message.
   - **Taps again later:** the customer is told the answer was already received. Nothing changes twice.
5. Prepaid (card) orders and orders without a phone number are skipped.

The bot never cancels orders, refunds money or changes stock. It only tags orders; you decide in Shopify.

Pakistani numbers are normalised automatically: `0300-1234567`, `03001234567`, `3001234567` and `+92 300 1234567` all become `923001234567`.

## What you need

| Item | Notes |
|---|---|
| n8n 2.x | n8n Cloud or self-hosted, reachable over HTTPS, with Data Tables available (built and tested on 2.49) |
| Shopify store | A custom app access token with `read_orders` and `write_orders` |
| WhatsApp Business Platform (Cloud API) | Meta app, business phone number, permanent access token, business account ID |

### WhatsApp costs

The confirmation request is a business-initiated **Utility template** message, so Meta charges for it per delivered message. Third-party sources put the Pakistan rate at about USD 0.015 per utility message from 1 October 2026; check Meta's current rate card. The thank-you replies are sent inside the 24-hour customer service window that opens when the customer taps a button; from 1 October 2026 those service messages are free up to a monthly free tier per number and charged after it.

## Step 1: Create the WhatsApp template

In WhatsApp Manager create a template:

- **Name:** `order_confirmation`
- **Category:** Utility
- **Language:** English (`en`), or change `template_language` in Store settings to match yours
- **Body** (5 variables, in this order):

```
Assalam o Alaikum {{1}}! Thank you for ordering from {{2}}.
Order {{3}}: {{4}}
Total: {{5}} (Cash on Delivery)
Please confirm so we can dispatch your order.
```

- **Buttons:** two **Quick reply** buttons, in this order: `Confirm order` (first) and `Cancel order` (second).

Wait for Meta to approve it. The workflow fills the variables as: 1 customer first name, 2 store name, 3 order number, 4 items, 5 total.

## Step 2: Create the Data Table

In n8n go to **Data tables → Create** and name it exactly `codConfirmations`. Add these columns, all type **string**:

`orderId, orderName, phone, customerName, items, total, shopTags, status, waMessageId`

(`id`, `createdAt` and `updatedAt` are added by n8n automatically.)

## Step 3: Import and configure

1. **Create workflow → ⋯ → Import from file** → `cod-order-confirmation-whatsapp.json`.
2. Open **Store settings**:
   - `store_name`: your store name as customers know it
   - `whatsapp_phone_number_id`: from Meta → WhatsApp → API setup ("Phone number ID")
   - `template_name` / `template_language`: as created in Step 1
   - `default_country_code`: `92` for Pakistan
   - `cod_only`: `true` to message only COD orders
3. Credentials:

| Node | Credential |
|---|---|
| Shopify Trigger, Tag order nodes | Shopify Access Token API (shop subdomain + access token + app secret) |
| Send confirmation request, Reply nodes | WhatsApp API (permanent token + business account ID) |
| WhatsApp Trigger | WhatsApp OAuth (Meta app ID + app secret) |

4. If a Data Table node shows "Currently no items exist" under its columns, press the reload button next to the columns. It's a display quirk; the mappings are saved.

## Step 4: Test, then go live

1. Publish the workflow.
2. Place a COD test order in your store with your own WhatsApp number.
3. You should receive the template within seconds. Tap **Confirm order**.
4. Check: the order in Shopify has the tag `COD Confirmed`, the Data Table row says `confirmed`, and you got the thank-you message.
5. Place a second test order and tap **Cancel order**. Then cancel the test orders in Shopify.

## How it was tested

Built for n8n 2.49 and run on a local n8n 2.49 with a real Data Table. Shopify and WhatsApp calls were replaced by stand-ins (no real accounts), every other node ran for real:

| Test | Result |
|---|---|
| New COD order (`0300-1234567`) | Template request built for `923001234567` with button payload `CONFIRM:5550001`; row saved as `pending` |
| Paid card order | Skipped, nothing sent |
| Customer taps Confirm | Order found, tags `Online, COD Confirmed`, row `confirmed`, thank-you text correct |
| Same customer taps again | "Already received" reply, nothing changed |
| Second COD order (`+92 321 7654321`) then Cancel | Tag `COD Cancelled`, row `cancelled`, cancellation text correct |

Not covered by that test: the live Shopify webhook, Meta's template approval and real message delivery. Do Step 4 with your own accounts before turning it on for customers.

## Troubleshooting

| Problem | Fix |
|---|---|
| No WhatsApp message on a new order | Is the order COD with a phone number? Is the template approved and the name/language exactly as in Store settings? Check **Executions**. Rows with status `send_failed` show orders to call manually. |
| "Template name does not exist" | Name or language code differs from WhatsApp Manager. |
| Button tap does nothing | The WhatsApp Trigger must be active (published). The template's buttons must be Quick reply, Confirm first. |
| Order not tagged | The Shopify token needs `write_orders`. |
| Token expired | Use a permanent system user token, not the 24-hour test token. |

## Privacy

Order details and phone numbers are stored in your n8n Data Table. Only message customers who gave their number for this order, and delete old rows you no longer need.

## Licence

Built by [your agency name]. You may use and adapt this workflow for your own store. Reselling the workflow file itself is not allowed.
