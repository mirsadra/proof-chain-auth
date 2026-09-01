# proof-chain-auth

Serves **proof.parkinsad.com** — the canonical auth-callback and legal host for the
Proof Chain iOS app.

## Why this is a separate repo

GitHub Pages allows only **one** custom domain per repository (the `CNAME` file).
`proofapp.site` is served by [`proof-chain-web`](https://github.com/mirsadra/proof-chain-web)
and must keep serving its own `apple-app-site-association`, because every copy of the
app already shipped to the App Store has `applinks:proofapp.site` compiled into its
entitlements. Apple's AASA fetcher does **not** follow redirects, so folding both
domains into one repo would silently break Universal Links for existing installs.

Both domains therefore run in parallel. `proofapp.site` can be retired only once the
App Store versions that reference it are no longer in use.

## Contents

| Path | Purpose |
|---|---|
| `.well-known/apple-app-site-association` | Universal Links association for `G9QU83M3D9.com.parkinsad.Proof` |
| `auth/callback/` | Landing page for Supabase auth redirects (the app intercepts this URL before the page loads) |
| `privacy-policy.html` | Linked from the App Store listing and the in-app paywall |
| `terms-of-service.html` | Linked from the App Store listing and the in-app paywall |
| `help.html` | Support page linked from in-app Settings |

## Gotchas

- **`_config.yml` must keep `include: [.well-known]`.** Jekyll skips dot-directories by
  default; without this the AASA file is never published and Universal Links stop
  working with no visible error.
- **The AASA file must be served as-is** — no redirect, no `.json` extension, and
  GitHub Pages already sends it as `application/json`.
- Support mail stays on `hi@proofapp.site`, which has working iCloud MX records.
  Do not repoint it without first creating the alias in iCloud Custom Email Domain.

## Deploying

Pushing to `main` triggers the Pages workflow in `.github/workflows/deploy.yml`.
