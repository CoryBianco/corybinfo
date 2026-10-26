# coryb.info

My living resume. It's plain HTML and CSS in `public/` with no build step and no dependencies.

## Editing

- All the content is in `public/index.html`.
- Under Experience, each role shows its first few bullets. Give any other bullet `class="extra"` and it goes behind a **Show N more** button. Extra bullets always appear when the page is printed.
- Update the "Now" section and the "Last updated" date in the footer whenever you change something.
- Colors and fonts are tokens at the top of `public/styles.css`, with a light and a dark set. Headings use Fraunces and body text uses Inter, both from Google Fonts.
- The "CB" circle at the top is a placeholder for a photo. Save one as `public/avatar.jpg` and swap it in using the comment above the monogram in `index.html`.
- **Save as PDF** prints the page. The print styles at the bottom of `styles.css` turn it into a black-on-white resume of about two pages, without the "Now" section.
- The phone number from the PDF resume is left off on purpose, because this page is public.

## Previewing locally

The quickest way is Python's built-in server:

```bash
python -m http.server 8787 --directory public
```

Then open http://localhost:8787.

To preview exactly what Cloudflare serves, including the headers from `_headers` and the 404 page, use Wrangler instead. It needs Node.js 22 or newer.

```bash
npx wrangler dev
```

## Deploying

The site runs on Cloudflare Workers as a static-assets Worker named `corybinfo`, built from the `CoryBianco/corybinfo` GitHub repo.

**Every push to `main` deploys the site.** Cloudflare runs `npx wrangler deploy`, which reads `wrangler.jsonc`:

- `assets.directory` serves `public/` as the site root, so `README.md` and the rest of the repo stay private.
- `not_found_handling: "404-page"` serves `public/404.html` for missing pages.
- `name` must stay `corybinfo` to match the Worker in the dashboard.

| Address | What it is |
| --- | --- |
| https://coryb.info | Production (custom domain on the Worker) |
| https://www.coryb.info | Production (custom domain on the Worker) |
| https://corybinfo.corybdev.workers.dev | The Worker's own URL |

To deploy from your machine instead of pushing, run `npx wrangler login` once, then `npx wrangler deploy`.

## DNS

Squarespace is still the registrar for coryb.info, but its nameservers point at Cloudflare (`brett.ns.cloudflare.com` and `laura.ns.cloudflare.com`), so all DNS is managed in Cloudflare.

| Name | Type | Target | Notes |
| --- | --- | --- | --- |
| `coryb.info` | Worker custom domain | `corybinfo` | Created by Cloudflare. Manage it from the Worker's **Domains** tab, not by hand. |
| `www.coryb.info` | Worker custom domain | `corybinfo` | Same as above |
| `quacks` | CNAME | `m95iaw1x.up.railway.app` | Quacks on Railway. Keep it **DNS only**, so Railway handles its own HTTPS and WebSockets. |
| `_railway-verify.quacks` | TXT | `railway-verify=…` | Railway's ownership check for the Quacks domain |

There are no MX records, so nothing here affects email.

**DNSSEC is currently off.** To turn it on, enable it under **DNS → Settings** in Cloudflare, then add the DS record Cloudflare shows on Squarespace's **DNSSEC** page. Never leave a DS record at Squarespace that doesn't match the nameservers in use: that takes the whole domain offline, Quacks included.
