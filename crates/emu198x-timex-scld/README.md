# emu198x-timex-scld

The Timex SCLD (Semi-Custom Logic Device) of the TC2048, TC2068 and TS2068.
It has the 48K's contention, eight video modes through port `$FF`
(including dual-screen, hi-colour and 512-pixel hi-res) and full I/O
decoding.

It implements the `Ula` trait from `emu198x-bus-sinclair-zx-spectrum` over the
shared video engine in `emu198x-sinclair-zx-spectrum-ula`, and adds the
variant's own contention and port decoding. The machine ticks it every
half-cycle before the CPU and reads its pins between ticks: no bus callbacks,
the same as every Emu198x chip. It renders palette indices; RGBA conversion is
a separate stage.

## Provenance

Part of [Emu198x](https://github.com/emu198x/emu198x), a family of cycle-accurate
retro-computing emulator cores. This crate is published so siblings and outside
projects can use it on its own; it is versioned independently of the
Emu198x suite and bumps only when it changes.

Licensed GPL-2.0-or-later.

## Save-state compatibility

Version 0.2 preserves the video engine’s pending fetches, border output latch, VidEN and falling-edge phase/address in serialized state. Its positional binary layout differs from 0.1. Callers must version their enclosing save format and reject older payloads before decoding; missing pipeline state cannot be recovered exactly. The Rust stepping API and hardware algorithms are unchanged. The owner must still reattach its immutable timing configuration after restore.
