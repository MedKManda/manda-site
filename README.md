# MandaNetwork — The Language & Knowledge Lab

Marketing site for MandaNetwork. Plain static HTML/CSS/JS — no build step, no framework
runtime required. Originally exported from a Next.js app; rebuilt here as a lean static
site since only the rendered output (not the Next.js source) was available.

## Structure

```
index.html                     Single-page site
assets/css/styles.css          Compiled Tailwind CSS (from the original build)
assets/js/main.js               Mobile nav toggle + contact form submission
assets/img/                    Team photos, board photos, logo (resized/compressed)
favicon.png                    Site favicon
.htmlvalidate.json             Config for the HTML lint step in CI
.github/workflows/validate.yml CI: HTML lint + broken-link check on every push/PR
```

## Local preview

No build tools needed — any static file server works:

```bash
npx serve .
# or
python3 -m http.server 8000
```

Then open the printed URL in a browser.

## CI (GitHub Actions)

`.github/workflows/validate.yml` runs on every push/PR to `main` and lints `index.html`
with [html-validate](https://html-validate.org/) and checks for broken links with
[lychee](https://github.com/lycheeverse/lychee-action). It's a safety check only —
it does not deploy anything.

## Deployment (Coolify, self-hosted)

Production hosting is **Coolify**, self-hosted on a Hetzner server, reached through a
**Cloudflare Tunnel** (Zero Trust) rather than a public IP — the server has no exposed
inbound ports.

### One-time setup

1. In Coolify, create a new **Application** sourced from this GitHub repo
   (`MedKManda/manda-site`, branch `main`). Use the **Static** build pack with the
   publish directory set to `.` (the repo root — `index.html` lives there directly, no
   build step needed).
2. Connect Coolify to the repo (deploy key or GitHub App, whichever Coolify's "new
   resource" flow prompts for) and enable **auto-deploy on push** — Coolify manages its
   own webhook on the GitHub repo for this, no manual step needed on the GitHub side.
3. In Coolify's app settings, add `mandanetwork.ai` (and `www.mandanetwork.ai` if you
   want both) as the app's domain.
4. In **Cloudflare Zero Trust → Networks → Tunnels → (the tunnel this Coolify server
   already uses)**, add a **Public Hostname** route for each:
   - Domain: `mandanetwork.ai` (leave the subdomain field blank for the apex)
   - Service: the internal address Coolify's app tab shows for this resource
     (e.g. `http://<container>:<port>`)
   - Repeat with subdomain `www` for `www.mandanetwork.ai`, same service.

   Adding a Public Hostname route in the Tunnel config creates/updates the matching
   Cloudflare DNS record automatically (a proxied `CNAME` to
   `<tunnel-id>.cfargotunnel.com`) — no manual DNS edit needed for these two hostnames.
   If a conflicting record already exists on `www` or the apex from a previous host,
   remove it first so Cloudflare can create the tunnel-routed one.
5. Leave the domain's `MX`/`TXT` mail records (Google Workspace) untouched — they're
   unrelated to this and unaffected either way.

GitHub Pages is not used for production. (It's fine to leave a repo's Pages site
disabled/unconfigured; nothing here depends on it.)

## Contact form (Formspree)

The form in `index.html` posts to a [Formspree](https://formspree.io) endpoint.
`assets/js/main.js` submits it via `fetch` and shows an inline success/error message
without leaving the page. The CI link checker excludes `formspree.io` URLs since it's a
POST-only endpoint that isn't meaningful to GET/HEAD-check as a regular link.

To change which Formspree form it submits to, update the `action` attribute on
`#contact-form` in `index.html` (search for `formspree.io/f/`) with the ID from your
Formspree dashboard.

## Known issue carried over from the source export

The original export has the **Nihel Zaied** and **Ismail Khlifi** team photos swapped
(the file that renders under Nihel's name is captioned `ismail` in the original asset
names, and vice versa). This revision preserves the original visual output as-is
(`assets/img/team-nihel-zaied.jpg` / `assets/img/team-ismail-khlifi.jpg` are named for
who they *display as*) — worth double-checking with the team on which photo is
actually correct.
