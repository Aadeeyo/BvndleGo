# BvndleGo

Website for BvndleGo: GoCar, GoBus and GoCharge. **Move Better.**

Everything is in a single file, `index.html`, with no build step. It is published with GitHub Pages.

## Fill in before launch
Near the top of the `<script>` in `index.html` there is a `CONFIG` block:

```js
const CONFIG = {
  coverage: 'Starting in Lagos',
  support: { phone: '', whatsapp: '', email: '', hours: '' }
};
```

- **support**: add verified phone, WhatsApp, email and support hours. They appear automatically in the "Contact & support" panel. While these are empty, the panel sends people to the request form instead.
- **coverage**: update it as cities and routes go live.

## Still needed from the business
- **Forms**: connect the request flow and the forms on each page to the CRM or a form endpoint. They are front-end only at the moment.
- **Indicative prices, deposits, rental requirements, cancellation and refund terms**: the FAQs currently say these are "shared with your quote".
- **Verified charging locations** (address, hours, connectors, pricing) and **GoBus routes** (stops, times, fares).
- **Bvndle Coins**: earning rates, expiry and eligibility rules.
- **Trust proof**: insurance, driver vetting, vehicle standards, customer feedback and client logos.
- **Response-time commitment**: the confirmation screen currently says "the team will contact you".
