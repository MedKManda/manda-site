# MandaNetwork — The Language & Knowledge Lab

Marketing site for MandaNetwork. Plain static HTML/CSS/JS — no build step, no framework
runtime required. Originally exported from a Next.js app; rebuilt here as a lean static
site since only the rendered output (not the Next.js source) was available.

## Structure

```
index.html              Single-page site
assets/css/styles.css   Compiled Tailwind CSS (from the original build)
assets/js/main.js       Mobile nav toggle + contact form submission
assets/img/             Team photos, board photos, logo (resized/compressed)
favicon.png             Site favicon
CNAME                   Custom domain for GitHub Pages
.nojekyll                Disables Jekyll processing on GitHub Pages
.htmlvalidate.json       Config for the HTML lint step in CI
.github/workflows/deploy.yml   CI/CD: validate, then deploy to GitHub Pages
```

## Local preview

No build tools needed — any static file server works:

```bash
npx serve .
# or
python3 -m http.server 8000
```

Then open the printed URL in a browser.

## CI/CD pipeline (GitHub Actions)

`.github/workflows/deploy.yml` runs on every push/PR to `main`:

1. **validate** — lints `index.html` with [html-validate](https://html-validate.org/) and
   checks for broken links with [lychee](https://github.com/lycheeverse/lychee-action).
2. **build** — packages the site as a Pages artifact (only after validation passes).
3. **deploy** — publishes to GitHub Pages (only on push to `main`, not on PRs).

### One-time repo setup

1. Push this repo to GitHub.
2. In the repo settings, go to **Settings → Pages** and set **Source** to
   **GitHub Actions**.
3. Push to `main` (or run the workflow manually from the Actions tab) to trigger the
   first deploy.

### Custom domain (mandanetwork.ai)

A `CNAME` file pointing at `mandanetwork.ai` is already included. To finish wiring up
DNS with your registrar:

- Apex domain (`mandanetwork.ai`): add four `A` records pointing to GitHub Pages' IPs:
  `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`.
- `www.mandanetwork.ai`: add a `CNAME` record pointing to `<your-github-username>.github.io`.
- In **Settings → Pages**, confirm the custom domain and enable **Enforce HTTPS** once
  DNS has propagated (GitHub provisions the certificate automatically).

If you'd rather deploy to the default `https://<username>.github.io/<repo>/` URL instead,
delete the `CNAME` file and remove the custom domain from the Pages settings.

## Contact form (Formspree)

The form in `index.html` posts to `https://formspree.io/f/YOUR_FORM_ID`. To activate it:

1. Create a free account at [formspree.io](https://formspree.io) and add a new form.
2. Replace `YOUR_FORM_ID` in `index.html` (search for `formspree.io/f/`) with your real
   form ID.
3. `assets/js/main.js` submits the form via `fetch` and shows an inline success/error
   message without leaving the page.

Until the real ID is in place, the link checker step in CI is configured to skip that
placeholder URL so it won't fail the build.

## Known issue carried over from the source export

The original export has the **Nihel Zaied** and **Ismail Khlifi** team photos swapped
(the file that renders under Nihel's name is captioned `ismail` in the original asset
names, and vice versa). This revision preserves the original visual output as-is
(`assets/img/team-nihel-zaied.jpg` / `assets/img/team-ismail-khlifi.jpg` are named for
who they *display as*) — worth double-checking with the team on which photo is
actually correct.
