# domestica.mx

The public site of Doméstica (Organización Blacktrex, S.A. de C.V.): a
landing page for families and for people looking for work, the two monday
intake forms embedded, and the legal pages. Plain HTML and CSS, no build step,
no tracking. Copy is Spanish; code, comments and commits are English.

| Page | Path |
|---|---|
| Landing | `index.html` |
| Family request form (embedded) | `solicitud.html` → `domestica.mx/solicitud` |
| Worker registration form (embedded) | `registro.html` → `domestica.mx/registro` |
| Aviso de privacidad | `privacidad.html` (noindex) |
| Condiciones del servicio | `condiciones.html` (noindex) |

## Deploy

GitHub Pages from `main`, root folder. A push publishes in about a minute
(`gh api repos/tommtj/domestica-site/pages/builds/latest`).

DNS lives at GoDaddy (nameservers `ns45/ns46.domaincontrol.com`):

- `A @` → `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
- `CNAME www` → `tommtj.github.io`
- **Do not touch** the email records: `MX` `mx1/mx2.improvmx.com` (ImprovMX
  forwards `contacto@domestica.mx`), `TXT` SPF `v=spf1 include:spf.improvmx.com ~all`,
  and `TXT _dmarc`.

GoDaddy asks for a Google Authenticator code on every DNS change.

## Traps we already hit

- **Stylesheet cache.** Pages serves files with `max-age=600`, so a browser
  keeps an old `styles.css` for 10 minutes and new markup renders unstyled.
  Every page loads `styles.css?v=<version>`; **bump the version in every HTML
  file whenever `styles.css` changes.**
- **HTTPS certificate.** It was not issued until the custom domain was removed
  and re-added (`PUT /pages` with `{"cname": null}`, then `{"cname":
  "domestica.mx"}`; an empty string does not remove it). Each of those
  API calls makes GitHub commit to `CNAME`, so `git pull --rebase` before
  pushing. Certificate: Let's Encrypt, apex + `www`, auto-renewed.
- **Embedding monday forms.** Only `forms.monday.com/forms/embed/<id>` may be
  framed (the plain form URL sends `frame-ancestors` for monday only). monday
  sends no height, so the iframe fills the screen below the header and
  scrolls on its own. Keep the "Ábrelo aquí" fallback link to the standalone
  form: uploads inside iframes are unreliable in some in-app browsers. Form
  ids: families `28356a23a0ead652eed82471efdc04dd` (`wkf.ms/4jfbMWo`),
  workers `63d23540245c74c504f0bca350933242` (`wkf.ms/4dyAI7B`).
- **Search Console.** `googlef65387e7f06f08e5.html` at the root verifies the
  URL-prefix property `https://domestica.mx/` (added
  2026-10-19). **Do not delete or rename it**: Google re-checks it and the
  property is unverified without it. Sitemap submitted there; update
  `sitemap.xml` (and its `lastmod`) when a page is added.
- **Link preview.** `assets/og.png` (1200×630) was rendered from an HTML card
  with headless Chrome (`--headless=new --window-size=1200,630 --screenshot`).
  WhatsApp caches previews per URL; old shares keep the old card.

## Legal pages

Before changing `privacidad.html` or `condiciones.html`, read the notes in the
private project repository; the legal texts are not edited here on their own.

Fonts: Quicksand (OFL, `assets/OFL-Quicksand.txt`). Icons: Lucide (ISC,
`assets/LICENSE-lucide.txt`).
