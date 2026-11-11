# joshaico gadget

Device firmware and the on-device SDK for Joshaico Gadget boards.

The gateway side - device hub, protocol v2, STT, dashboard - lives with the
Joshaico agent, not here.

## Wire protocol

`joshaico-gadget.v2`

- Devices authenticate with an HMAC-SHA256 challenge over a per-device key
  derived from a key the board holds; the gateway never receives the key.
- Turns are text in, reply (with streamed deltas) out.
- Audio streams both ways in sequenced binary frames, so transcription and
  speech happen server-side.
- Device actions (an LED, a relay, a timer) are requested by the gateway and
  answered by the device.

Firmware for the ESP boards is the next thing to land here.
