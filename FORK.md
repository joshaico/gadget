# Joshaico Gadget firmware

This tree is the stock [Hermes Gadget SDK](https://github.com/Adolanium/hermes-gadget-sdk)
(MIT, v0.5.0) imported as the base for the Joshaico Gadget firmware. It holds
the device firmware, the browser installer, and the plugin copy the tests pin.
Joshaico's own hub, adapter, STT and dashboard are a fork of that plugin living
in `core/plugins/platforms/gadget/` in the agent repository.

## Wire protocol

The stock firmware speaks `hermes-gadget.v1`. This fork speaks
`joshaico-gadget.v2`:

| | Stock | This fork |
|---|---|---|
| Subprotocol | `hermes-gadget.v1` | `joshaico-gadget.v2` |
| `hello.proto` | `1` | `2` |
| Device ids | `hg-` + 16 hex of SHA-256(key) | `jg-` + the same |
| Auth context | `hermes-gadget/v1\|` | `joshaico-gadget/v2\|` |
| OTA context | `hermes-gadget/v1\|ota\|` | `joshaico-gadget/v2\|ota\|` |
| `welcome.server` | `hermes` | `joshaico` |

The rest is the stock handshake and message set, plus the face system that
sends and holds expression gifs. The constants live in
`firmware/core/include/hg/protocol.hpp` and `plugin/protocol.py`; both test
suites pin the same identity, auth and OTA vectors.

## What is still stock

- The Python package and CLI are still named `hermes_gadget` /
  `hermes-gadget`, so the firmware keeps reporting the same
  `esp_app_desc_t.project_name` and OTA accepts the same images.
- `docs/` and `site/` still carry upstream prose and page names beyond the
  installer entry point and the protocol page, which are rebranded.
- Board profiles, drivers and artwork are unchanged.

## Status

The v2 port is complete as of the import: firmware, the plugin copy, the
desktop simulator and every pinned test vector speak `joshaico-gadget.v2`,
and the installer installs the images this tree builds. The gadget page in
Joshaico points at `https://gadget.joshai.co/installer.html`.

## Hosting the installer

The repository is private, and GitHub Pages cannot publish a private repository
on a free plan, so the built site is served from `gadget.joshai.co` by a static
server on the host, reached through the existing `wsl-agent` Cloudflare Tunnel.

- Server: `gadget-installer.service` (user unit) runs
  `python3 -m http.server 9131` over `~/.joshaico/gadget-installer/site`.
- Tunnel: `~/.cloudflared/config.yml` routes the `gadget.joshai.co` hostname to
  `127.0.0.1:9131`; the DNS CNAME came from
  `cloudflared tunnel route dns wsl-agent gadget.joshai.co`.
- Build: `.github/workflows/installer.yml` uploads the site as an artifact with
  the latest release's firmware. To publish a build, copy it over the served
  directory and the server picks it up on the next request.

```bash
cd site && node scripts/build.mjs --out ~/.joshaico/gadget-installer/site
```

Ingress is first-match, so a new hostname rule goes above the
`service: http_status:404` catch-all, and cloudflared must be restarted
(`systemctl restart cloudflared-named.service`) to read the file.
