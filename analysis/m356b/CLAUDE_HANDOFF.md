# Claude handoff: SM-M356B

## Objective

Port Samsung Galaxy M35 5G `SM-M356B`, codename `m35x`, firmware
`M356BXXU8DZE3`, CSC `ZTO`, Android 16/API 36, Exynos 1380, reported kernel
`5.15.189-android13-3-33470412`.

## Current state

The offline investigation is complete as far as the available files permit.
The target scaffold is at `src/targets/m35x-M356BXXU8DZE3/`. It is deliberately
blocked by a preprocessor `#error`; no runnable payload is claimed. The six
requested reports are in `analysis/m356b/`.

## Evidence

The workspace contains no exact M356B firmware or kernel components. Searches
found only the pre-existing root notes `M356BXXU8DZE3_ANALYSIS.md` and
`M356BXXU8DZE3_VERIFICATION.md`. Missing items include boot/vendor boot,
Image/kernel, ELF, BTF/DWARF, config, DTB/DTBO, BL/sboot, ramdisk properties,
tracefs data, symbols, `Module.symvers`, payload, and KernelSU assets.

The exact PR was fetched from `zainarbani/Root-My-Galaxy-Payloads` as
`refs/remotes/zainarbani/pr-3`. Six commits were inspected individually:
`50de045` configfs safety probe, `94ecec9` FPSIMD route, `026a357` A546E
artifact replacement, `98ccbf7` scratch/UMH offsets, `cafc6bd` A546E profile,
and `87b9399` URL updates.

## Values and provenance

- Identity fields are **INFERRED** from the user request only.
- M356B kernel-dependent values are **UNKNOWN**.
- No M356B value is **VERIFIED** or **DERIVED**.
- A546E values in `M356B_COMPARISON.md` are PR reference values only.
- No values are silently copied.

## A546E versus M356B

The kernel release string matches A546E exactly, but A546E's `KIMAGE_TEXT_BASE`,
physical load assumptions, symbols, BTF layouts, trace event/caller, slide
fingerprint, route, and page collision tuning are target evidence. The PR
itself records A546E-specific changes, including offsets that differ between
A546E firmware revisions by `0x180`, and a 125-entry Image-derived fingerprint.
Exynos 1380 is not proven equivalent to A546E.

## Files modified/created

Created:

- `src/targets/m35x-M356BXXU8DZE3/README.md`
- `src/targets/m35x-M356BXXU8DZE3/target.h`
- `src/targets/m35x-M356BXXU8DZE3/p0_fingerprint.h`
- `analysis/m356b/M356B_PORT_STATUS.md`
- `analysis/m356b/M356B_EVIDENCE.md`
- `analysis/m356b/M356B_COMPARISON.md`
- `analysis/m356b/M356B_UNKNOWN.md`
- `analysis/m356b/M356B_BUILD_REPORT.md`
- `analysis/m356b/CLAUDE_HANDOFF.md`

Pre-existing M356B root notes were preserved. No unrelated changes were
reverted.

## Artifacts

Existing `.so`, `.ko`, and `ksud` files belong to other targets. They are not
M356B artifacts and must not be published for M356B. M356B artifact directory,
payload, KernelSU module, loader, raw kernel, and P0 fingerprint are absent.

## Build/tests

Direct clang preprocessing reaches the new target and fails with the expected
missing-artifact message. The Makefile invocation could not start because the
container has no Android NDK. No payload build, JSON publication, device test,
flashing, unlock, or irreversible action occurred.

## Next steps

1. Obtain exact AP/BL firmware and preserve provenance/hash.
2. Extract and validate properties, boot kernel, Image header, BTF, ELF,
   symbols, disassembly, DTB/DTBO, config, and module metadata.
3. Derive every unknown value independently; generate and verify M356B P0
   fingerprint.
4. Replace the guard with a complete target header and compile with NDK.
5. Build/audit exact KernelSU assets, add artifact(s), validate feed schema and
   sizes, then perform separate controlled hardware validation.
