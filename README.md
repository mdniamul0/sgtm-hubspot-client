# HubSpot Client by MD Niamul — Server-side GTM

A free, open-source **server-side Google Tag Manager (sGTM) Client** that receives **HubSpot webhooks**, fetches the latest contact properties from the HubSpot CRM API, and runs your server container with that data.

Built by **MD Niamul** — Data Analytics Architect, founder of [Digital Soldier Agency](https://mdniamul.com) and official Stape partner.

> Turn CRM lead-status changes into accurate offline conversions for Google Ads, Meta CAPI, GA4 and more — raw truth, no manipulated metrics.

## What it does

1. Listens on a request path you choose (for example `/hubspot`) and accepts `POST` requests only.
2. Reads the HubSpot webhook batch (for example a `contact.propertyChange` on `hs_lead_status`).
3. Replies `200 Ok` to HubSpot right away so the webhook is not retried.
4. For each event, calls `GET https://api.hubapi.com/crm/v3/objects/contacts/{objectId}` with the properties you pick.
5. Runs the server container once per event, so your tags can send offline conversions.

## Event data

| Key | Source |
|---|---|
| `event_name` | Event name set in the client |
| `contactId` | HubSpot `objectId` |
| `lead_status` | Webhook `propertyValue` |
| `properties` | Contact properties from the HubSpot API (e.g. `properties.email`) |
| `property_name` | Webhook `propertyName` |
| `subscription_type` | Webhook `subscriptionType` |
| `occurred_at` | Webhook `occurredAt` (milliseconds) |
| `event_id` | Webhook `eventId` — use for de-duplication |

## Installation

**From the Community Template Gallery:** Server container → Templates → Client Templates → **Search Gallery** → "HubSpot Client by MD Niamul" → Add to workspace.

**Manual:** download `template.tpl` → Templates → **New** → ⋮ → **Import**.

## Setup

1. **HubSpot:** create a private app with the scope `crm.objects.contacts.read` and copy the access token.
2. In the app's **Webhooks** settings, set the target URL to `https://YOUR-SGTM-DOMAIN/hubspot` and subscribe to the event you need (for example contact property change → `hs_lead_status`).
3. **GTM server container:** Clients → New → HubSpot Client by MD Niamul. Fill in:
   - **Request path:** `/hubspot` (same as the webhook URL; use a hard-to-guess path)
   - **Event name:** e.g. `hubspot_lead_status`
   - **HubSpot access token**
   - **Contact properties to fetch:** e.g. `email`, `phone`, `firstname`, `lastname`, `hs_lead_status`
4. Create triggers on `Event Name equals hubspot_lead_status` (and `lead_status` values) for your conversion tags.
5. Publish the server container.

## How to verify

1. Open **Preview** in the server container.
2. Change a contact's lead status in HubSpot (or send a test from the webhook settings).
3. Confirm the client claimed the request, the HubSpot API request returned `200`, and your event shows `properties` in Event Data.

## Security notes

- Keep the request path hard to guess — anyone who knows it can trigger contact lookups.
- The access token only needs read access to contacts.
- HTTP permission is limited to `https://api.hubapi.com/*`.

## Support

Bugs and feature requests: open an issue in this repository.
Custom server-side or CRM conversion tracking: [calendly.com/mdniamul](https://calendly.com/mdniamul) · [mdniamul.com](https://mdniamul.com) · [LinkedIn](https://www.linkedin.com/in/mdniamul)

## License

[Apache License 2.0](LICENSE)
