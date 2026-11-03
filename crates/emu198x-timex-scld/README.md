# emu198x-timex-scld

The Timex SCLD (Semi-Custom Logic Device) of the TC2048, TC2068 and TS2068.
It has the 48K's contention model and the documented port `$FF` video modes:
primary and secondary 256-pixel screens, 8×1 attributes, and 512-pixel mode 6.
Encodings 3, 4, 5 and 7 are reserved in the manufacturer mode table; their
existing output remains unqualified.

It implements the `Ula` trait from `emu198x-bus-sinclair-zx-spectrum` over the
shared video engine in `emu198x-sinclair-zx-spectrum-ula`, and adds the
variant's own contention and port decoding. The machine ticks it every
half-cycle before the CPU and reads its pins between ticks. It renders palette indices; RGBA conversion is
a separate stage.

## Provenance

Part of [Emu198x](https://github.com/emu198x/emu198x), a family of cycle-accurate
retro-computing emulator cores. This crate is published so siblings and outside
projects can use it on its own; it is versioned independently of the
Emu198x suite and bumps only when it changes.

Licensed GPL-2.0-or-later.

## Save-state compatibility

Version 0.2 preserves the video engine’s pending fetches, border output latch, VidEN and falling-edge phase/address in serialized state. Its positional binary layout differs from 0.1. Callers must version their enclosing save format and reject older payloads before decoding; missing pipeline state cannot be recovered exactly. The Rust stepping API and hardware algorithms are unchanged. The owner must still reattach its immutable timing configuration after restore.


The next patch release requires ULA 0.2.1 for corrected mode-6 column order,
complementary paper and border, and disabled bright/flash. These follow the
TS2068 Technical Manual, Table 3.2.2-1 and §5.2.3. No serialized fields change,
but corrected execution changes pixels; Emu198x's owning Timex runtimes use
snapshot v18 and reject v17 rather than promise the old continuation.

`frame_timing()` reports the reattached PAL or TS2068 configuration, matching
the hardware counters. The additive `TIMING_TS2068` constant lets owners share
that metadata. Existing callers need no stepping API changes and must continue
to reattach their board's immutable configuration after restore.
