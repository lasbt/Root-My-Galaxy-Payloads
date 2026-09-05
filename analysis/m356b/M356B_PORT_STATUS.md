# M356B port status

## Target

- Model: `SM-M356B`
- Codename: `m35x`
- Firmware/AP: `M356BXXU8DZE3`
- CSC: `ZTO`
- Android: 16 / API 36
- SoC: Exynos 1380
- Kernel reported by the request: `5.15.189-android13-3-33470412`

## Status

**Static port implemented; hardware validation pending.** The exact firmware
was obtained from Samsung FUS, and the target header/fingerprint were derived
from its kernel Image, ELF and BTF. No payload artifact or support-feed entry
was published because the Android NDK and runtime evidence are unavailable.

The A546E PR proves that equal kernel release strings do not make every symbol,
structure layout, route, or physical address portable; M356B-specific values
were recalculated where the Image/ELF/BTF permitted.

## Available locally

- Existing target headers and P0 fingerprints for other devices.
- A546E PR #3 recovered from `zainarbani/Root-My-Galaxy-Payloads`, including all
  six requested commits.
- Existing payloads, KernelSU binaries, loaders, source, build files, and
  `docs/PORTING.md`.
- Official FUS package and exact M356B kernel/Image/BTF/ELF.
- Existing root-level M356B notes, which were preserved.

## Missing locally

BL/sboot, kernel config/`Module.symvers`, tracefs runtime dumps, and a
M356B-specific payload or KernelSU module remain unavailable.

## Implementation decision

Created:

- `src/targets/m35x-M356BXXU8DZE3/README.md`
- `src/targets/m35x-M356BXXU8DZE3/target.h`
- `src/targets/m35x-M356BXXU8DZE3/p0_fingerprint.h`

`target.h` now contains the statically derived M356B profile and
`p0_fingerprint.h` contains 32 Image-derived candidates. No critical offset was
copied from A546E. `support/targets-v3.json` was not modified because it must
only advertise downloadable, buildable and tested artifacts.

## Verification

The host-clang preprocessor check passes and editor diagnostics report no
errors. The normal Makefile check stops because `ANDROID_NDK_HOME` is
unavailable. No flashing, unlock, exploit execution, or irreversible operation
was performed.

See `M356B_EVIDENCE.md`, `M356B_COMPARISON.md`, `M356B_UNKNOWN.md`, and
`M356B_BUILD_REPORT.md` for the detailed record.
