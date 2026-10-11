# BANAN Store

Arabic, right-to-left mug storefront built with HTML, CSS, and vanilla JavaScript. The catalog is read from a published Google Sheets CSV; customers can browse products and prepare an order for WhatsApp.

## Try the sample catalog

Requires Python 3 for the local server and a modern browser. There is no npm install or build step.

```bash
git clone https://github.com/basilcod/BANAN-Store.git
cd BANAN-Store
python -m http.server 8080 --bind 127.0.0.1
```

Open **http://127.0.0.1:8080/?demo=1**. On Windows, use `py` instead of `python` if needed.

Demo mode reads the included sample CSV, keeps its cart and wishlist separate from normal browsing, and disables WhatsApp/order API submission. Fonts, icons, and PapaParse still load from public CDNs, so an internet connection is needed. Use fictional customer details during a walkthrough.

## Features

- Arabic RTL layout with light/dark themes.
- Search, category filters, stock filter, and price sorting.
- Product details, related items, and product links using `?p=PRODUCT_ID`.
- Cart and wishlist persistence in browser local storage.
- Client-side coupon calculations and stock-aware cart quantities.
- WhatsApp checkout handoff and a Google Apps Script order submission hook.

## Stack and files

| Path | Purpose |
| --- | --- |
| `index.html` | Storefront markup, styles, catalog loading, cart, and checkout |
| `demo/products.csv` | Sample catalog used by `?demo=1` |
| `mugs/` | Product photographs and background assets |
| `admin.html` | Legacy in-browser product-list editor; not a secured admin service |
| `tests/` | Node regression checks for demo isolation and inline event encoding |

## Catalog configuration

Normal mode uses the `sheetURL`, `ORDERS_API`, and `WHATSAPP_NUMBER` constants in `index.html`. Use endpoints you own before connecting a separate deployment. Browser-delivered configuration is public; it must never contain a secret.

The CSV loader skips the first row and reads columns **by position**, in this order:

| Column | Meaning | Example |
| --- | --- | --- |
| `id` | Stable unique product ID | `mug-1` |
| `name` | Display name | `Coffee mug` |
| `price` | Numeric price in ILS | `25` |
| `category` | Category key | `cheap`, `medium`, `premium` |
| `image` | Image URL or relative path | `mugs/jpeg (1).jpeg` |
| `stock` | Available quantity | `8` |
| `description` | Product description | `Sample mug` |

Do not publish customer/order information in the catalog sheet. Google Apps Script source and server-side validation are not included in this repository.

## Walkthrough

1. Open `?demo=1` and confirm the demo banner.
2. Search for a mug, filter categories, and change price sorting.
3. Add an available mug to the cart and adjust its quantity.
4. Add a favorite, reload, and verify demo state persists.
5. Open checkout: submitting shows a demo message without sending an order.

## Checks

With Node.js 20 or later:

```bash
node --test
```

## Current boundaries

This is a storefront prototype, not a payment processor. Prices and coupons in the browser are not authoritative. In normal mode, the Apps Script request uses `no-cors`, so the frontend cannot verify server acceptance; opening WhatsApp does not confirm a completed order.

The password comparison in `admin.html` is visible in client code and provides no access control. That page edits an in-memory list and exports JavaScript; it does **not** update the Google Sheets catalog used by the storefront. Use the spreadsheet to maintain the current catalog.

Next engineering steps: authenticated server-side administration, authoritative stock/pricing, acknowledged order creation, and accessible keyboard navigation for all dialogs.
