# M356B evidence

## Evidence levels

- **VERIFIED**: directly observed in a local file or command result.
- **DERIVED**: calculated from an exact M356B artifact by a documented method.
- **INFERRED**: supplied by the request or a technically plausible comparison;
  not sufficient for a payload.
- **UNKNOWN**: no exact evidence is available.

## Identity

| Field | Value | Evidence | Status |
|---|---|---|---|
| Model | `SM-M356B` | FOTA ramdisk properties | VERIFIED |
| Codename | `m35x` | FOTA ramdisk properties | VERIFIED |
| Firmware | `M356BXXU8DZE3` | FUS response and FOTA | VERIFIED |
| CSC | `ZTO` / `M356BOWO8DZE3` | FUS response | VERIFIED |
| Android/API | `16 / 36` | FOTA fingerprint and metadata | VERIFIED |
| SoC | Exynos 1380 | user request | INFERRED |
| Kernel release | `5.15.189-android13-3-33470412` | kernel version string | VERIFIED |
| Architecture | ARM64 | `file`/ELF | VERIFIED |
| Full Android fingerprint | `samsung/m35xxx/essi:16/BP4A.251205.006/M356BXXU8DZE3:user/test-keys` | FOTA | VERIFIED |

## Artifact inventory

The exact package and selected components were obtained outside git under
`/tmp/m356b-dze3`. The 8.40 GiB package was not added to the repository.

| Component | Present | Consequence |
|---|---:|---|
| `boot.img`/compressed boot | yes | exact kernel extracted and hashed |
| `vendor_boot.img` | no | no vendor ramdisk metadata |
| raw `Image`/`kernel` | no | no Image header or P0 fingerprint |
| `vmlinux`/ELF/DWARF | no | no symbols/disassembly |
| BTF | no | no structure sizes/bit offsets |
| DTB/DTBO | no | no platform/load corroboration |
| BL/sboot | no | no physical load proof |
| config/`Module.symvers` | no | no module ABI or security config |
| ramdisk/properties | no | no verified build identity |
| tracefs/runtime dump | no | no event ID or caller verification |

## PR #3 evidence

The exact PR was fetched from `zainarbani/Root-My-Galaxy-Payloads` as
`refs/remotes/zainarbani/pr-3`. The requested commits were inspected
individually:

1. `50de045` adds a configfs read probe before the CFI read/write sequence. If
   the probe fails, the payload exits and restores the P0 oracle rather than
   risking an unrecoverable hijacked `misc_fops`. This is shared control flow,
   not an M356B offset.
2. `94ecec9` changes A546E profiles from `SLIDE_ROUTE_MCAST` to
   `SLIDE_ROUTE_FPSIMD`. It changes route selection, not the proof that M356B
   can use that route.
3. `026a357` replaces A546E app `.so` binaries. It contains no source-level
   derivation and the binary is not compatible evidence for M356B.
4. `98ccbf7` moves scratch `FOPS_OFF` from `0x2000` to `0x7000` and root UMH
   work/data from `0x6000/0x6200` to `0x7800/0x7a00` to avoid overlap. These are
   exploit-page choices for the A546E profile, not kernel symbols.
5. `cafc6bd` adds A546E Android 16 profile data, a 125-entry P0 fingerprint,
   app artifact, and support-feed entry. Its commit message records hashes and
   hardware testing, none of which applies to M356B.
6. `87b9399` changes artifact URLs in the support feed. It does not add
   compatibility logic or M356B evidence.

The PR also contains A546E's exact build fingerprint, Image-derived offsets,
BTF-confirmed layouts, and hardware result. Those are reference data only.
