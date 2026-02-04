# Ecommerce Proof of Concept: Lemon Squeezy vs Stripe

**Date:** 2026-02-04
**Context:** Evaluating ecommerce approaches for physical and digital products, with a focus on the Maggie Mistal project
**Stack:** Next.js 15 (App Router) + Sanity CMS + Tailwind + Vercel

---

## Executive Summary

**Recommendation: Use Stripe for the Maggie Mistal project (and as the standard for product-one projects).**

Lemon Squeezy was the original default in the product-one stack, but three factors have shifted the calculus:

1. **Lemon Squeezy cannot sell physical products** -- Maggie has physical workbook versions that need checkout support
2. **Stripe acquired Lemon Squeezy in July 2024** -- the platform's future is uncertain, SDK is stale, support is degrading
3. **Stripe Managed Payments** (built by the LS team inside Stripe) is entering public access in 2026, offering MoR benefits without LS's limitations

Stripe costs less per transaction (2.9% + $0.30 vs 5% + $0.50), supports both physical and digital goods, and has industry-leading documentation and Next.js integration patterns.

---

## Maggie Mistal's Ecommerce Needs

### Digital Products (8 items)

| Product | Type | Delivery |
|---------|------|----------|
| Soul Search Workbook | PDF download | Instant |
| Research Workbook | PDF download | Instant |
| Job Search Workbook | PDF download | Instant |
| 3-Workbook Bundle | PDF bundle download | Instant |
| Five Elements Assessment | Interactive/PDF | Instant |
| Career Empowerment Webinar | Video access | Instant |
| How-To Book (ebook version) | PDF/ePub download | Instant |
| Physical workbook versions | Shipped print | Fulfillment required |

### Non-Ecommerce Revenue (handled separately)

- **Discovery calls** (30min, free) -- Calendly
- **Coaching sessions** (60min, paid) -- Calendly
- **Executive coaching** -- Calendly + direct sales
- **Speaking inquiries** -- Contact form
- **Mastermind** -- Application-based
- **Florida Keys Retreat** -- Contact form

### Key Requirements

1. **Physical + digital products** in one checkout system
2. **Simple checkout** -- current WooCommerce is 5 steps (high abandonment)
3. **Visible pricing** -- current site hides prices until cart view
4. **Tax handling** -- digital products have VAT/sales tax obligations
5. **Low friction** -- "Buy Now" should be 1-2 clicks to payment
6. **Sanity CMS integration** -- product catalog managed in Sanity, payment links stored as fields

---

## Platform Comparison

### Feature Matrix

| Capability | Stripe | Lemon Squeezy |
|------------|--------|---------------|
| **Physical products** | Full support (shipping, tax) | Not supported |
| **Digital products** | Supported (DIY delivery) | Native delivery built in |
| **Subscriptions** | Full support (Stripe Billing) | Supported |
| **Payment methods** | 100+ methods, 135+ currencies | 21 methods |
| **Tax handling** | Stripe Tax (add-on, $0.50/txn) | Included (MoR) |
| **Checkout UX** | Hosted, embedded, or custom | Hosted or overlay |
| **Product catalog API** | Full CRUD | Read-only (dashboard only) |
| **Webhook support** | Comprehensive | Supported |
| **Next.js SDK** | Official, actively maintained | Stale (~1 year old) |
| **Documentation** | Industry gold standard | Good but stagnating |
| **Long-term viability** | Core business | Uncertain (acquired) |

### Cost Comparison

| Scenario | Stripe | Lemon Squeezy |
|----------|--------|---------------|
| $25 digital product | $1.03 (4.1%) | $1.75 (7.0%) |
| $49 workbook bundle | $1.72 (3.5%) | $2.95 (6.0%) |
| $15 ebook | $0.74 (4.9%) | $1.25 (8.3%) |
| $1,000/month in sales | ~$35-40 | ~$60-70 |
| $5,000/month in sales | ~$175-195 | ~$300-350 |

*Stripe figures include base 2.9% + $0.30. Lemon Squeezy is 5% + $0.50. Stripe Tax adds $0.50/txn if enabled.*

### The Lemon Squeezy Problem

Despite being the product-one default, Lemon Squeezy has several issues:

1. **No physical products** -- Hard blocker for Maggie's printed workbooks
2. **Acquired by Stripe (July 2024)** -- Being absorbed into Stripe Managed Payments
3. **Degraded support** -- Multiple reports of slow responses post-acquisition
4. **SDK staleness** -- `@lemonsqueezy/lemonsqueezy.js` v4.0.0, last updated ~1 year ago
5. **Reliability concerns** -- Reports of 500 errors, failed payments, corrupt Stripe price IDs
6. **No programmatic product creation** -- Products must be created in dashboard, not API
7. **Higher fees** -- Nearly double Stripe's rate on each transaction

### What Lemon Squeezy Does Better

1. **Zero tax burden** -- As MoR, they handle all sales tax/VAT filing and remittance
2. **Built-in digital delivery** -- File hosting, download links, license keys out of the box
3. **Faster initial setup** -- ~2 hours vs ~2 days for comparable Stripe setup
4. **Simpler mental model** -- One dashboard for products, payments, delivery, and analytics

---

## Recommended Architecture: Stripe for Maggie Mistal

### Product Catalog Flow

```
Sanity CMS (content) ──→ Next.js (display) ──→ Stripe Checkout (payment)
                                                      │
                                                      ▼
                                               Stripe Webhook
                                                      │
                                    ┌─────────────────┼─────────────────┐
                                    ▼                                   ▼
                            Digital Delivery                    Physical Fulfillment
                         (email download link)              (shipping notification)
```

### Sanity Schema for Products

Products are managed in Sanity (descriptions, images, pricing display) with Stripe Price IDs stored as references:

```typescript
// sanity/schemas/product.ts
export default {
  name: 'product',
  title: 'Product',
  type: 'document',
  fields: [
    { name: 'title', type: 'string' },
    { name: 'slug', type: 'slug', options: { source: 'title' } },
    { name: 'description', type: 'text' },
    { name: 'image', type: 'image' },
    { name: 'price', type: 'number', description: 'Display price in dollars' },
    {
      name: 'productType',
      type: 'string',
      options: { list: ['digital', 'physical', 'bundle'] }
    },
    {
      name: 'stripePriceId',
      type: 'string',
      description: 'Stripe Price ID (e.g., price_xxx). Create in Stripe Dashboard first.'
    },
    {
      name: 'digitalFile',
      type: 'file',
      description: 'For digital products: the downloadable file',
      hidden: ({ parent }) => parent?.productType === 'physical'
    },
    { name: 'featured', type: 'boolean' },
    { name: 'order', type: 'number' },
  ]
}
```

### Checkout via Server Action

```typescript
// app/actions/checkout.ts
'use server'

import Stripe from 'stripe'
import { redirect } from 'next/navigation'

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!)

export async function createCheckoutSession(formData: FormData) {
  const priceId = formData.get('priceId') as string
  const productType = formData.get('productType') as string

  const sessionConfig: Stripe.Checkout.SessionCreateParams = {
    mode: 'payment',
    line_items: [{ price: priceId, quantity: 1 }],
    automatic_tax: { enabled: true },
    success_url: `${process.env.NEXT_PUBLIC_URL}/purchase/success?session_id={CHECKOUT_SESSION_ID}`,
    cancel_url: `${process.env.NEXT_PUBLIC_URL}/products`,
  }

  // Collect shipping for physical products
  if (productType === 'physical') {
    sessionConfig.shipping_address_collection = {
      allowed_countries: ['US', 'CA'],
    }
    sessionConfig.shipping_options = [
      { shipping_rate: 'shr_standard' },
    ]
  }

  const session = await stripe.checkout.sessions.create(sessionConfig)
  redirect(session.url!)
}
```

### Webhook Handler

```typescript
// app/api/webhooks/stripe/route.ts
import Stripe from 'stripe'
import { headers } from 'next/headers'

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!)

export async function POST(request: Request) {
  const body = await request.text()
  const headersList = await headers()
  const sig = headersList.get('stripe-signature')!

  let event: Stripe.Event
  try {
    event = stripe.webhooks.constructEvent(
      body, sig, process.env.STRIPE_WEBHOOK_SECRET!
    )
  } catch (err) {
    return new Response(`Webhook Error: ${err}`, { status: 400 })
  }

  if (event.type === 'checkout.session.completed') {
    const session = event.data.object as Stripe.Checkout.Session

    // Determine product type from metadata or line items
    // For digital: generate signed download URL, send email
    // For physical: trigger fulfillment notification
  }

  return new Response('OK', { status: 200 })
}
```

### Digital Delivery Strategy

For Maggie's digital products (workbooks, assessment, webinar), the simplest approach:

1. **Files stored in Sanity** (or Vercel Blob / Cloudflare R2 for larger files)
2. **On purchase**: webhook generates a signed, time-limited download URL
3. **Email via Resend**: send download link to customer's email
4. **Success page**: also displays download link immediately after purchase
5. **Optional**: store purchases in a simple database for re-download capability

This replaces what Lemon Squeezy does natively, but gives full control over the experience.

### Environment Variables

```env
# .env.local
STRIPE_SECRET_KEY=sk_test_...
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_...
STRIPE_WEBHOOK_SECRET=whsec_...
```

---

## Proof of Concept Options

### Option A: Stripe Payment Links (Fastest, No Code)

**Effort:** ~1 hour | **Best for:** Validating demand before building anything

1. Create products in Stripe Dashboard (test mode)
2. Generate Payment Links for each product
3. Store Payment Link URLs in Sanity product documents
4. Render "Buy Now" buttons that link to Stripe Payment Links
5. No webhook needed -- Stripe handles confirmation emails

**Limitation:** No custom success page, no digital delivery automation, no physical product shipping collection.

### Option B: Stripe Checkout + Server Actions (Recommended PoC)

**Effort:** ~1-2 days | **Best for:** Real proof of concept with full flow

1. Create products/prices in Stripe Dashboard (test mode)
2. Store Stripe Price IDs in Sanity product schema
3. Build Server Action for creating Checkout Sessions
4. Build webhook handler for `checkout.session.completed`
5. Build success page that retrieves session details
6. Add email delivery for digital products (Resend)

This gives a working end-to-end flow: browse products in Sanity-powered pages, click buy, pay via Stripe Checkout, receive digital product via email.

### Option C: Full Embedded Checkout (Production-Ready)

**Effort:** ~3-5 days | **Best for:** Production launch

Everything in Option B, plus:
- Embedded Checkout (stays on Maggie's site, no redirect)
- Cart functionality (bundle multiple products)
- Order history / re-download page
- Physical product shipping address collection
- Stripe Tax enabled for automatic tax compliance
- Customer portal for subscription management (if Mastermind becomes a subscription)

---

## Proof of Concept Scope (Recommended: Option B)

### What to Build

1. **Sanity product schema** with Stripe Price ID field
2. **Products page** (`/products`) displaying all products from Sanity
3. **Product detail page** (`/products/[slug]`) with "Buy Now" button
4. **Server Action** to create Stripe Checkout Session
5. **Webhook route** to handle `checkout.session.completed`
6. **Success page** (`/purchase/success`) with download link for digital products
7. **Email notification** via Resend with download link

### What to Skip (for PoC)

- Cart/multi-item checkout (just single-product purchase)
- User accounts / order history
- Physical product fulfillment automation
- Stripe Tax (enable later)
- Subscription billing
- Embedded checkout (use hosted redirect)

### Files to Create

```
app/
  products/
    page.tsx                    # Product listing
    [slug]/
      page.tsx                  # Product detail + buy button
  purchase/
    success/
      page.tsx                  # Post-purchase confirmation + download
  actions/
    checkout.ts                 # Server Action: create Checkout Session
  api/
    webhooks/
      stripe/
        route.ts                # Stripe webhook handler
lib/
  stripe.ts                     # Stripe client singleton
sanity/
  schemas/
    product.ts                  # Product document type (add stripePriceId field)
```

---

## Migration Path from Current WordPress/WooCommerce

Maggie's current site uses WooCommerce with a multi-step checkout that causes abandonment. The migration:

| Current (WooCommerce) | New (Stripe Checkout) |
|------------------------|-----------------------|
| 5-step checkout process | 1-click → Stripe hosted page |
| Prices hidden until cart | Prices visible on product pages |
| WooCommerce cart management | No cart needed (direct checkout) |
| WooCommerce order management | Stripe Dashboard + webhooks |
| Manual tax handling | Stripe Tax (when enabled) |
| Plugin-dependent delivery | Custom email delivery via Resend |

---

## Stripe Managed Payments: Future Consideration

Stripe is building **Stripe Managed Payments (SMP)** using knowledge from the Lemon Squeezy acquisition. Key differences from Lemon Squeezy:

- **Transaction-level MoR** -- can apply MoR to some products and not others
- **Within your Stripe account** -- no separate platform, same dashboard
- **Tax filing included** -- Stripe handles remittance
- **Broader payment support** -- inherits Stripe's 100+ methods
- **Gradual adoption** -- no all-or-nothing commitment

**Current status:** Private preview, expanding to public access in 2026.

**Recommendation:** Build on Stripe now. When SMP goes public, enabling it could be as simple as adding a configuration flag to your Checkout Sessions for the tax/compliance benefits, without changing the rest of your integration.

---

## Decision: Update Product-One Default

### Previous Default
- Lemon Squeezy for digital goods

### Recommended New Default
- **Stripe Checkout** for all ecommerce (physical + digital)
- **Stripe Tax** for tax compliance (enable when ready)
- **Resend** for delivery emails
- **Sanity** for product catalog content (with Stripe Price IDs as references)
- **Stripe Managed Payments** when publicly available (optional MoR upgrade)

### Why Change
1. Lemon Squeezy's future is uncertain post-acquisition
2. Stripe covers physical + digital (LS is digital only)
3. Lower per-transaction cost
4. Better SDK maintenance and documentation
5. Stripe is the foundation LS runs on anyway
6. SMP will eventually offer LS's MoR benefits natively in Stripe

---

## Next Steps

1. **Create Stripe test account** (or use existing) for Maggie Mistal project
2. **Add products in Stripe Dashboard** (test mode) with prices for all 8 products
3. **Build PoC (Option B)** -- products page, checkout action, webhook, success page
4. **Test end-to-end** -- purchase a digital product, verify email delivery
5. **Test physical product** -- verify shipping address collection works
6. **Evaluate Stripe Tax** -- enable `automatic_tax` and verify tax calculations
7. **Update product-one README** -- change default ecommerce from Lemon Squeezy to Stripe

---

## Sources

### Stripe
- [Stripe Pricing](https://stripe.com/pricing)
- [Stripe Checkout Docs](https://docs.stripe.com/payments/checkout)
- [Stripe Tax](https://stripe.com/tax)
- [Stripe Products & Prices](https://docs.stripe.com/products-prices/how-products-and-prices-work)
- [Stripe Webhooks](https://docs.stripe.com/webhooks)
- [Stripe + Next.js Guide (Pedro Alonso)](https://www.pedroalonso.net/blog/stripe-nextjs-complete-guide-2025/)
- [Your Next Store Template](https://vercel.com/templates/next.js/yournextstore)

### Lemon Squeezy
- [Lemon Squeezy Docs](https://docs.lemonsqueezy.com)
- [Lemon Squeezy 2026 Update](https://www.lemonsqueezy.com/blog/2026-update)
- [Official Next.js Billing Template](https://github.com/lmsqueezy/nextjs-billing)
- [JS SDK (npm)](https://www.npmjs.com/package/@lemonsqueezy/lemonsqueezy.js)

### Comparisons
- [Stripe vs Lemon Squeezy (AnotherWrapper)](https://anotherwrapper.com/blog/stripe-vs-lemonsqueezy-saas)
- [Transaction Fee Comparison (UserJot)](https://userjot.com/blog/stripe-polar-lemon-squeezy-gumroad-transaction-fees)
- [Stripe Managed Payments Analysis (UserJot)](https://userjot.com/blog/stripe-managed-payments-for-saas)
