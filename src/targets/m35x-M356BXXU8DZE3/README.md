# SM-M356B M356BXXU8DZE3

This target is a statically derived port profile. It is not hardware validated
and no support-feed artifact is published yet.

The exact M356B kernel, ELF, BTF and P0 fingerprint were recovered from the
official FUS firmware outside the repository. Critical symbol and layout
values were derived from those artifacts rather than copied from SM-A546E.
Bootloader/runtime-dependent values and an Android NDK build remain pending.

See `analysis/m356b/` for the evidence, comparison matrix, build report, and
handoff notes.
