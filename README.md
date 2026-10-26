# coryb.info

My living resume. It's plain HTML and CSS in `public/` with no build step and no dependencies.

## Editing

- All the content is in `public/index.html`.
- Under Experience, each role shows its first few bullets. Give any other bullet `class="extra"` and it goes behind a **Show N more** button. Extra bullets always appear when the page is printed.
- Update the "Now" section and the "Last updated" date in the footer whenever you change something.
- Colors are tokens at the top of `public/styles.css`, with a light and a dark set.
- **Save as PDF** prints the page. The print styles at the bottom of `styles.css` turn it into a black-on-white resume of about two pages, without the "Now" section.
- The phone number from the PDF resume is left off on purpose, because this page is public.

## Previewing locally

```bash
python -m http.server 8787 --directory public
```

Then open http://localhost:8787.

## Deploying to Cloudflare Pages

### 1. Push to GitHub

Create an empty repository, for example `CoryBianco/coryb.info`, then run this from this folder:

```bash
git init
git add .
git commit -m "First version of coryb.info"
git branch -M main
git remote add origin https://github.com/CoryBianco/coryb.info.git
git push -u origin main
```

### 2. Create the Pages project

In the Cloudflare dashboard, go to **Workers & Pages → Create → Pages → Import an existing Git repository** and pick the repo.

| Setting | Value |
| --- | --- |
| Framework preset | None |
| Build command | *(leave empty)* |
| Build output directory | `public` |

The first deploy gives you a `*.pages.dev` URL. After that, every push to `main` redeploys the site.

### 3. Move coryb.info's DNS to Cloudflare

Cloudflare Pages can only serve the bare domain (`coryb.info`) when Cloudflare runs the domain's DNS. Right now it's on Google's nameservers (`ns-cloud-b1` through `b4.googledomains.com`).

These are the records as of 2026-10-26:

| Name | Type | Value | What to do |
| --- | --- | --- | --- |
| `coryb.info` | A | `73.76.253.85` | Delete. Pages replaces it. |
| `www.coryb.info` | A | `73.76.253.85` | Delete. Pages replaces it. |
| `quacks.coryb.info` | CNAME | `m95iaw1x.up.railway.app` | Keep, set to **DNS only** |
| `_railway-verify.quacks.coryb.info` | TXT | `railway-verify=…` | Keep |

There are no MX records, so moving the DNS can't affect email.

`73.76.253.85` looks like a home Comcast IP, and it doesn't answer web requests. If something at home still uses it, like a game server or SSH, move that to its own name such as `home.coryb.info` first.

1. In Cloudflare, go to **Add a domain**, enter `coryb.info` and choose the Free plan. It imports your existing records. Check that the Quacks CNAME and the Railway TXT record came across.
2. Set the `quacks` CNAME to **DNS only** (grey cloud). Railway then keeps handling its own HTTPS and WebSockets.
3. Delete the two `73.76.253.85` A records.
4. Cloudflare shows you two nameservers. At the registrar for coryb.info (Squarespace Domains, if it was bought through Google Domains), replace the four `ns-cloud-b*.googledomains.com` entries with those two. If DNSSEC is on, turn it off at the registrar first.
5. Wait for Cloudflare to mark the domain **Active**. This usually takes minutes but can take a few hours.

### 4. Attach the domain

In the Pages project, go to **Custom domains → Set up a custom domain** and add `coryb.info`, then add `www.coryb.info` the same way. Cloudflare creates the DNS records and the HTTPS certificate.

Afterwards, check that https://coryb.info and https://quacks.coryb.info both load.
