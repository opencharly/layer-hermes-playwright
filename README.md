# hermes-playwright

Playwright Chromium layer for the Hermes AI agent image.

The `hermes-playwright` candy bakes the Chromium browser into a Hermes image for
headless browser automation. It installs the browser via
`npx playwright install chromium` into
`/tmp/.cache/ms-playwright` (`PLAYWRIGHT_BROWSERS_PATH`) and adds the Fedora
system libraries Chromium needs to launch (nss, gtk3, alsa-lib, and more).

Playwright's own `--with-deps` flag does not support Fedora (it falls back to
Ubuntu's `apt-get`), so this candy installs the system dependencies via rpm
packages and only the browser binary via npm.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `hermes-playwright` |
| Requires | `pod-hermes` |
| Browser | Chromium under `/tmp/.cache/ms-playwright` |
| Environment | `PLAYWRIGHT_BROWSERS_PATH=/tmp/.cache/ms-playwright` |
| Security | `shm_size: 1g` |
| Install files | `charly.yml`, `package.json` |
| Service / port | none |

## How to use it

Compose the layer by pinning this repo in a box's `candy:` list:

```yaml
my-hermes:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/pod-hermes:v2026.243.1016'
      - '@github.com/opencharly/layer-hermes-playwright:v2026.243.1050'
```

After the image is built, launch Chromium headlessly from Node.js:

```bash
charly shell hermes-playwright -c "NODE_PATH=~/.npm-global/lib/node_modules node -e \"
const { chromium } = require('playwright');
(async () => {
  const b = await chromium.launch({ headless: true });
  const p = await b.newPage();
  await p.goto('https://example.com');
  console.log(await p.title());
  await b.close();
})();
\""
```

## Layout

- `charly.yml` — the `hermes-playwright:` candy entity: the `pod-hermes`
  require, the `env:` browser path, the Fedora `distro:` package list, the
  `check:` assertions, and the embedded `skill:` entity.
- `package.json` — pins the Playwright npm package.
- `CHANGELOG/` — per-CalVer release notes.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-hermes:hermes-playwright` — the Playwright Chromium layer
- Runtime parent: `/charly-hermes:hermes` (the base agent box)
- Sibling: `/charly-hermes:playwright-layer` (OpenClaw AI snapshots)
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
