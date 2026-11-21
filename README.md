# Telegram WEB Proxy — clean personal build

Clean, deployment-oriented copy of `C0neF/Telegram-WEB-Cloudflare-Workers`
at upstream commit `457c83a71ab506812bf87661658ab7ef2b8d02a1`.

## Layout

- `app/src/` — Worker entry, Durable Object relay, bridge and MTProxy code.
- `shared/telegram-dc.js` — fixed Telegram WSS hosts for DC1..DC5.
- `wrangler.toml` — Worker, Durable Object binding and migration.
- `app/package.json` — only the pinned `wrangler` dependency and deploy scripts.

Validation, runtime-test, probe, test and CI artifacts are intentionally
removed from this clean branch. The upstream commit remains the provenance
source.

## Deploy

```sh
npm --prefix app ci
npx wrangler secret put PROXY_SECRET --config wrangler.toml
npm --prefix app run deploy:dry
npm --prefix app run deploy
```

`PROXY_SECRET` must be a strong random 16-byte secret in hex, or `dd` plus
the same 16 bytes. Do not use examples from documentation.

After deploy:

```sh
curl https://<host>/readyz
```

## Security boundary

This is a single-user personal WEB proxy, not a public multi-user service.
The bridge capability is static for a given host and secret; anyone with the
capability URL can create new bootstrap tokens until `PROXY_SECRET` is
rotated. Keep the URL private, use a strong secret, and put rate limiting in
front of `/?bridge=`.

The clean branch keeps the upstream DC1..DC5 allowlist and Telegram WSS
construction. It does not verify actual Cloudflare egress to DC1 or Telegram
Desktop/Android E2E behavior.

## Provenance and audit

- Upstream: <https://github.com/C0neF/Telegram-WEB-Cloudflare-Workers>
- Pinned commit: `457c83a71ab506812bf87661658ab7ef2b8d02a1`
- Audit reports: `E:\Work\NRSP\SimilarDocuments\.review\cf-audit\c0nef-*.md`
