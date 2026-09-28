# Souqora

Static e-commerce storefront (HTML5 + Tailwind CSS + vanilla JS) ready for GitHub Pages, with a clean layer for Square. Tagline: *Your One-Stop Shop for Effortless and Enjoyable Shopping.*

## Structure
```
index.html shop.html product.html cart.html checkout.html about.html contact.html
css/style.css        design system (buttons, cards, badges, fields)
js/config.js         public settings (currency, shipping, apiBase)
js/square.js         ALL Square/backend calls (the only file to change for integration)
js/products.js       demo products + product card
js/cart.js           cart + localStorage ("souqoraCart")
js/checkout.js       checkout form logic
js/app.js            header/footer, pages, search/filter/sort
robots.txt sitemap.xml CNAME .env.example
```

## Run locally
Open a terminal in the folder and run `python3 -m http.server 8000`, then visit http://localhost:8000. (Opening files by double-click also mostly works.) Works with no backend: demo products, working cart, demo order confirmation.

## Tailwind
Pages load Tailwind through its CDN script, which works on GitHub Pages with no build step. For maximum performance later, compile it: install Tailwind CLI, add `tailwind.config.js` (colors `brand #0F4C3A`, `saffron #E8A317`, `ink #12211B`, `paper #F7F8F6`) and replace the CDN `<script>` tags with the compiled CSS file.

## Connect Square (later)
Architecture: `Browser → Souqora frontend → your serverless API → Square APIs`.
1. Create a Square Developer app and use the **sandbox** first.
2. Build a small serverless API (Cloudflare Workers, Netlify or Vercel Functions) with three endpoints: `GET /catalog` (Catalog + Inventory APIs), `POST /checkout` (Orders API + Checkout API `CreatePaymentLink`, returning `{orderId, checkoutUrl}`), `GET /orders/:id`.
3. Store credentials as **environment variables/secrets** on that service (see `.env.example`).
4. Set `apiBase` in `js/config.js` to the API URL and enable CORS for `https://souqora.live` only.
5. Adjust `Square.mapItem()` in `js/square.js` to your API's response. Cart, pages and cards need no changes.
6. The backend must re-price the order from Square. Never trust prices sent by the browser.

## Security: what stays private
Never commit or put in HTML/JS: Square **access tokens**, **OAuth/application secrets**, webhook signature keys, or any payment credential. Only the public Square application ID and location ID (for the Web Payments SDK) may appear in frontend code.

## Deploy on GitHub Pages
1. Create a GitHub repository (e.g. `souqora`) and push:
   `git init && git add . && git commit -m "Souqora" && git branch -M main && git remote add origin https://github.com/YOUR_USER/souqora.git && git push -u origin main`
2. Repository → **Settings → Pages** → Source: **Deploy from a branch** → Branch `main`, folder `/ (root)` → Save.
3. Wait a minute; the site appears at `https://YOUR_USER.github.io/souqora/` (relative paths work in that subfolder).

## Custom domain: souqora.live
1. The `CNAME` file already contains `souqora.live`. In Settings → Pages → Custom domain, enter `souqora.live` and save.
2. At your domain registrar's DNS, add:
   - `A` records for `@` → `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
   - `CNAME` record for `www` → `YOUR_USER.github.io`
3. When DNS propagates, tick **Enforce HTTPS**. (Confirm IPs in GitHub's docs, as they can change.)

## Arabic / RTL readiness
Layout uses logical utilities (`ms-`, `me-`, `start-`, `end-`), so setting `dir="rtl"` on `<html>` works. Add translations later by moving UI text into a `js/i18n.js` dictionary.

## Before launch
Add `assets/images/og.png` (1200×630 social image), real product photos, a real contact-form service, and your legal pages (privacy, returns, terms).
