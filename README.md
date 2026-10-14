# BvndleGo

Website for BvndleGo: GoCar, GoBus and GoCharge. **Move smarter with BvndleGo.**

Everything is in a single file, `index.html`, with no build step. It is published with GitHub Pages. The brand follows the BvndleGo CI v1.0: the GO logo, GoCore Purple `#4A2467`, GoCore Pink `#E93C8E`, and DM Sans and Onest type.

## Settings (`CONFIG` at the top of the `<script>` in `index.html`)

| Key | What it does |
|---|---|
| `coverage` | Service-area line shown across the site |
| `support` | Phone, WhatsApp, email and hours for the Contact & support panel. When empty, the panel sends people to the request form |
| `googleMapsKey` | **Required for address search and drop-a-pin.** Use a browser key with *Maps JavaScript API*, *Places API (New)* and *Geocoding API* enabled, restricted to your domain (e.g. `aadeeyo.github.io/*`, `bvndlego.com/*`). Without it, people type addresses or share their device location |
| `mapRegion`, `mapCenter` | Bias address search to Nigeria and centre the map on Lagos |
| `vehicleTypes` | Options in the GoCar vehicle type dropdown |
| `chauffeurLatestEnd` | Latest end time for trips with a driver (default `21:00`) |
| `customerio` | Customer.io Data Pipelines JavaScript source (EU). Currently the **Staging** workspace source "BvndleGo Website" (id 95651). For go-live, create the same source in Production and swap `writeKey` |
| `submitUrl` | Optional extra HTTPS endpoint that also receives each request as a JSON POST |
| `tagCheckUrl` | Optional GET endpoint, `?tag=@name` → `{"available": true}`, for live Bvndle Tag availability |

## What reaches Customer.io
- `page` on load and on every section change
- `identify` on every request, with traits `name`, `email` (or `phone`), `bvndle_tag`, `company`, `preferred_contact`, `bvndlego_last_service`, `acquisition_source: bvndlego_website`. People are matched by email. Phone-only requests are identified as `bgo-phone-<digits>`, so automations still run for them
- `track` **`BvndleGo Request Submitted`**, with properties `reference`, `service` (car/bus/charge/biz), `status` (`new` / `on_hold_guarantor`), `guarantor_verification`, `bvndle_tag`, `bvndle_tag_requested`, plus every form field (pickup and coordinates, vehicle, guarantor details, contact method…)

**Live in Staging:** the automation *BvndleGo · New request alert to ops* (id 64) emails Rukiyat@bvndle.com the full details of every request. Build more automations on the same event, for example: alert the ops team, send the guarantor confirmation SMS or email when `guarantor_verification = guarantor_direct`, or notify the admin queue when it is `admin_tag_review`.

## Request payload (POST to `submitUrl`)

```json
{
  "reference": "BGO-XXXXXX",
  "service": "car | bus | charge | biz",
  "status": "new | on_hold_guarantor",
  "guarantor_verification": "guarantor_direct | admin_tag_review | null",
  "bvndle_tag": "@existing or null",
  "bvndle_tag_requested": "@newtag or null",
  "submitted_at": "ISO date",
  "source": "page URL",
  "fields": { "pickup": "...", "pickup_lat": 6.6, "pickup_lng": 3.35, "drive": "Self-drive", "vehicle": "SUV", "gname": "...", "gphone": "...", "gchannel": "SMS", "gtag": "...", "method": "WhatsApp", "...": "..." }
}
```

## Backend work needed for the new booking rules
- **Self-drive guarantor, direct confirmation**: when `guarantor_verification` is `guarantor_direct`, send the guarantor (`gname`, `gphone`, `gemail`) a confirmation link by `gchannel`, then move the booking from `on_hold_guarantor` to confirmable once they accept.
- **Self-drive guarantor, company or guarantor Tag**: when it is `admin_tag_review`, show the booking in the BvndleGo admin queue with `gtag` for approval.
- A self-drive booking must not be confirmed while it is `on_hold_guarantor`. The website shows the hold state, but the backend has to enforce it.
- **Bvndle Tag creation**: create the Tag in `bvndle_tag_requested` for the customer, using the contact details on the request.
- **9:00 PM limit for trips with a driver**: the form enforces it. Enforce it on the server too.

## Still needed from the business
Prices, deposits, rental terms, verified charging locations and GoBus routes, Coins earning rules, trust proof (insurance, vetting, client logos), and support contact details.
