# GTM sandbox site

A second, separate site — same structure as the main site (Home, About,
Products, Contact + 4 test products) — but with **no Brevo tracker**.
It's wired for **Google Tag Manager** instead.

## Files

- `index.html`, `about.html`, `contact.html` — the three main pages (the
  Brevo signup form is kept on Home and Contact; only the tracking script
  was swapped out).
- `products.html` — lists the 4 test products from `products-data.js`.
- `product.html` — product detail page; on load it pushes a
  `product_page_view` event to `window.dataLayer` with the product's
  details.
- `products-data.js` — the same 4 test products as the main site.
- `style.css` — same look and Brevo brand colors as the main site, minus
  the ext_id testing widget (not needed here).

## 1. Set up your GTM container

1. Go to [tagmanager.google.com](https://tagmanager.google.com) and create
   a container (or reuse an existing one) for this site.
2. You'll get a container ID that looks like `GTM-XXXXXXX`.
3. In **every** HTML file in this folder, replace the placeholder
   `GTM-XXXXXXX` with your real container ID — it appears twice per file:
   - once in the `<script>` in the `<head>`
   - once in the `<noscript><iframe ...>` right after `<body>`

   Easiest: open each file and use find-and-replace for `GTM-XXXXXXX`.

## 2. The custom event

`product.html` pushes this to the dataLayer on every product page view:

```js
window.dataLayer.push({
  event: "product_page_view",
  page_url: location.href,
  page_type: "product",
  product_id: product.id,
  product_name: product.name,
  product_price: product.price,
  product_currency: product.currency,
  product_image_url: product.image_url,
  product_category: product.category,
  product_discounted_price: product.discounted_price,
  product_in_stock: product.in_stock,
});
```

In GTM:

1. Create a **Custom Event trigger** matching event name `product_page_view`.
2. Create **Data Layer Variables** for each field you need (e.g.
   `product_id`, `product_name`, `product_price`, etc.) — variable name
   must match the dataLayer key exactly.
3. Attach those variables to whatever tag you want to fire (GA4 event tag,
   ads conversion tag, etc.), triggered by the Custom Event trigger above.
4. Use **Preview mode** in GTM to test: open the site with GTM Preview
   connected, click into a product page, and confirm `product_page_view`
   appears in the tag assistant with the right variable values.

## 3. Deploying this as its own GitHub Pages site

Same steps as the main site, just a separate repo (or a separate branch/
folder if you prefer, but a separate repo is simpler for a sandbox):

1. Create a new repository on GitHub (Public or Private — Pages works on
   both with a paid account).
2. Upload all the files in this folder to the repo (drag-and-drop via the
   GitHub web UI, or `git push` if you're comfortable with the CLI).
3. Go to **Settings → Pages**, set the source to the `main` branch, root
   folder, and save.
4. GitHub gives you a URL like `https://<username>.github.io/<repo-name>/`
   — that's your sandbox site.
