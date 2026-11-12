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
Joshaico points at `https://joshaico.github.io/gadget/installer.html`.
