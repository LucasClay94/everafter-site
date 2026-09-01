# Ever After — thisiseverafter.com

The public website for **Ever After** (the iOS app currently listed on the App
Store under its legacy name; see pickfair#394 for the rename). Static HTML, no
build step, no dependencies.

Built for pickfair#400: Google Play's business-authentication check needs a real
website, and the app needs a public home for its privacy policy, terms, and a
contact address. The primary job of the homepage is the App Store link;
everything else is utility beneath it.

## Structure

```
index.html          Hero + App Store CTA, what-it-is, contact
privacy/index.html  Privacy Policy      -> /privacy
terms/index.html    Terms of Service    -> /terms
support/index.html  Support + FAQ       -> /support
404.html            Not-found page
assets/styles.css   Shared stylesheet (design tokens mirror the app)
assets/             Wordmark, favicons, Open Graph card
CNAME               Custom domain for GitHub Pages
.nojekyll           Serve files as-is; do not run Jekyll
```

Legal pages are **directories, not flat `.html` files**, on purpose: `/privacy`
resolves on any static host without relying on extensionless-HTML rewriting. That
URL gets baked into App Store Connect metadata, which is version-locked and can
only be changed by shipping a new app version (pickfair#44) — so it has to be
right the first time and stay working if hosting ever moves.

## Editing

Open the HTML and edit it. There is no build, no bundler, no framework.

Preview locally:

```bash
cd ~/Developer/everafter-site && python3 -m http.server 4173
```

Then visit http://localhost:4173.

## Design

Tokens are lifted from the app's `tailwind.config.js` so the site and the app
read as one brand — warm near-black ground (`#161310`), cream type, gold accent,
Cormorant Garamond for display and Jost for UI.

One deliberate divergence: `--subtle` is lightened from the app's `#635D58` to
`#8C847C`. Every use of it here is small text, including the TMDb attribution,
and it has to clear WCAG AA (4.5:1) against both `--screen` and the lighter
`--surface` the footer sits on. The app's value fails that on the web.

## Careful: the legal text exists twice

`server/templates/privacy.html` and `terms.html` in the app repo are still served
at `api.pickfair.app/privacy` and `/terms`, and the **shipped app links there** —
so both copies are live and they can drift. The intended fix is to make this site
canonical and have the backend redirect to it, but only once this domain is
confirmed live (redirecting to a dead domain would break the legal links for
existing users and the App Store listing). Tracked in the app repo.

If you change the legal text here before that lands, change it in both places.

## Custom domain

`CNAME.pending` holds the custom domain. It is renamed to `CNAME` and committed
only **after** the Hover DNS records point at GitHub Pages — committing it early
makes Pages redirect the working `*.github.io` URL to a domain that does not yet
resolve, which looks like the site is down.
