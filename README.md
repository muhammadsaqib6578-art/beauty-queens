# Beauty Queens — E‑commerce template (Netlify + Stripe Checkout)

A fast, responsive cosmetics storefront with a cart, category filters, search, and live Stripe Checkout via a Netlify Function. Pure HTML/CSS/JS on the frontend; no frameworks required.

## Features
- Responsive storefront (hero, categories, new arrivals, best sellers, testimonials, newsletter)
- Product grid powered by simple JS data
- Cart drawer with add/remove/quantity and localStorage persistence
- Category filter and search
- Stripe Checkout (serverless) via Netlify Functions
- SEO basics (meta tags + social sharing), analytics-ready
- Easy to customize brand colors, products, and copy

## Tech stack
- Frontend: HTML, CSS, Vanilla JS
- Serverless: Netlify Functions (Node 18+)
- Payments: Stripe Checkout (stripe SDK in the function)

## Folder structure
```
.
├─ index.html
├─ styles.css
├─ script.js
├─ netlify.toml
├─ package.json
└─ netlify/
   └─ functions/
      └─ create-checkout-session.js
```

## Prerequisites
- Node.js 18+ installed
- Stripe account (for a test secret key: sk_test_…)
- Netlify account (free)

## Quick start (local)
1) Install deps:
```
npm install
```

2) Set your Stripe key in your shell for local dev (test mode):
- macOS/Linux:
```
export STRIPE_SECRET_KEY=sk_test_your_key_here
```
- Windows (PowerShell):
```
setx STRIPE_SECRET_KEY "sk_test_your_key_here"
```
Restart your terminal after setx on Windows.

3) Run with Netlify Dev (installs and emulates functions):
```
npm i -g netlify-cli
netlify login
netlify dev
```
Open http://localhost:8888, add items, and click Checkout. Use Stripe test card 4242 4242 4242 4242, any future expiry/CVC/ZIP.

## Deploy to Netlify (Git-based)
1) Push this folder to a GitHub repository.

2) In Netlify:
- Add new site → Import an existing project → choose your GitHub repo.
- Build command: leave empty
- Publish directory: `.`
- Functions directory: leave empty (netlify.toml already sets it) or set to `netlify/functions`

3) Environment variables (Site settings → Environment variables):
- STRIPE_SECRET_KEY = your Stripe test secret key (sk_test_…)
- Optional: NODE_VERSION = 18

4) Deploy (or trigger redeploy). Visit your site URL and test checkout.

## Configuration
- Brand and colors: edit CSS variables at the top of styles.css:
```
:root{
  --brand:#f15bb5; --brand-dark:#cf1391;
  --accent:#f15bb5; --accent-2:#7c3aed;
}
```

- Products: edit the products array in script.js (name, price, category, tags, image URL):
```js
const products = [
  {id:'p01', name:'Hydra Glow Serum', price:28, category:'Skincare', tags:['new'], img:'https://...'},
  // ...
];
```

- Currency: set in script.js where checkout is called (currency: 'usd'). Also ensure amounts are in the desired currency in your Stripe dashboard.

- Shipping countries: update in netlify/functions/create-checkout-session.js
```js
shipping_address_collection: { allowed_countries: ['US'] },
```
Add ISO country codes as needed, e.g. ['US','CA','GB','AU'].

- Success/Cancel redirect: handled in script.js
```js
success_url: `${location.origin}/?checkout=success`,
cancel_url: `${location.origin}/?checkout=cancelled`
```
The page shows a flash message and clears the cart on success.

- SEO: update <title>, <meta name="description">, and Open Graph tags in index.html.

- Analytics: paste your GA4/Pixel snippet into index.html.

## Security and pricing notes
- This demo sends price amounts from the browser to the serverless function. For production, validate prices server‑side.
- Recommended: use Stripe Price IDs instead of passing raw numbers:
```js
// Example (serverless) using predefined Prices in Stripe
line_items: [
  { price: 'price_12345', quantity: 2 },
  { price: 'price_67890', quantity: 1 }
]
```
Store a server-side catalog mapping your product IDs to Stripe Price IDs.

## Troubleshooting
- 500 error on checkout:
  - Ensure STRIPE_SECRET_KEY is set in Netlify’s environment variables
  - Check Functions → create-checkout-session → Logs in Netlify
  - Confirm package.json has "stripe" in dependencies and deploy picked it up

- 404 on function:
  - Path must be netlify/functions/create-checkout-session.js
  - netlify.toml must exist at repo root

- Cart doesn’t persist:
  - localStorage may be blocked in some privacy modes; test a normal browser profile

- Mixed content errors:
  - Ensure all image URLs are https

## License
Choose a license for your repo (MIT is common for templates). You can add one quickly by creating a LICENSE file or using GitHub’s “Add license” button.

## Acknowledgments
- Product images in the template use Unsplash placeholders. Replace with your own assets.
