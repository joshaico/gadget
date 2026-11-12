# Joshaico Gadget firmware

This tree is the stock [Hermes Gadget SDK](https://github.com/Adolanium/hermes-gadget-sdk)
(MIT, v0.5.0) imported as the base for the Joshaico Gadget firmware. The
gateway side - device hub, protocol v2, STT, dashboard - lives with the
Joshaico agent, not here.

## Wire protocol

The stock firmware speaks `hermes-gadget.v1`:

- Subprotocol `hermes-gadget.v1`, `proto: 1`
- Device ids `hg-` + the first 16 hex of SHA-256 of the device key
- Auth context `hermes-gadget/v1|`, OTA context `hermes-gadget/v1|ota|`

Joshaico's plugin is a fork of the same code renamed to `joshaico-gadget.v2`
(`jg-` ids, `joshaico-gadget/v2|` contexts). The two are otherwise the same
handshake and message set, so the port is mostly the constants in
`firmware/core/include/hg/protocol.hpp` plus the Joshaico face (`gif`) message.

## Status

The stock source is imported and unmodified. The v2 port has not started.
Release builds are still the stock ones at
`github.com/Adolanium/hermes-gadget-sdk/releases`.
