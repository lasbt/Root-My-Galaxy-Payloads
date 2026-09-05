# M356B autonomous progress

## 2026-09-05

- Confirmed public SamMobile index entry for `SM-M356B` / `ZTO` / `M356BXXU8DZE3`:
  https://www.sammobile.com/samsung/galaxy-m35/firmware/SM-M356B/ZTO/download/M356BXXU8DZE3/2000586/
- Queried Samsung FUS with public `samloader` clients. Exact response:
  `M356BXXU8DZE3/M356BOWO8DZE3/M356BXXU8DZE3/M356BXXU8DZE3`.
- Downloaded the official FUS package to `/tmp/m356b-dze3/M356BXXU8DZE3_ZTO.zip`.
- Package size: `9016478636` bytes (`8.40 GiB`). SHA-256:
  `01d5467f794259f5e2ac468cacdfcd5d27e13ba175fb064c192a3197eeaad63a`.
- Package contents include BL, AP, CP, CSC/HOME_CSC; AP listing confirms
  `vendor_boot.img.lz4`, `dtbo.img.lz4`, `meta-data/`, and `meta-data/fota.zip`.
  The proprietary package remains outside git.
- Public PR #3 was rechecked: all six commits are available; A546E commit
  `cafc6bd` reports the same kernel release/build number as the target, but
  also records A546E-specific offsets and hardware validation.
- Previous scaffold remains intentionally fail-closed until exact M356B kernel
  evidence is extracted and independently derived.

## Next automatic step

Extract only AP boot/vendor boot/dtbo and `meta-data/fota.zip`, validate their
hashes and metadata, then recover Image/BTF/ELF/symbol information where the
public firmware makes it possible.

## Kernel recovery completed

- Extracted `boot.img`, `init_boot.img`, `vendor_boot.img`, `dtbo.img` and
  `meta-data/fota.zip` to `/tmp/m356b-dze3`; none was added to git.
- `boot.img` is 67108864 bytes and contains a 39225856-byte ARM64 Image.
  The Image extracted from boot is byte-identical to FOTA `BOOT/kernel`.
- Kernel SHA-256:
  `0dbbdf35e8dd7caddb40365fb53a8c606fb084b0165a454ef4ed14c4ac0813b3`.
- Build fingerprint:
  `samsung/m35xxx/essi:16/BP4A.251205.006/M356BXXU8DZE3:user/test-keys`.
  `android-info.txt` confirms board `s5e8835`; ramdisk props confirm model
  `SM-M356B`, device `m35x`, SDK 33 for the boot image.
- `vmlinux-to-elf` recovered `vmlinux.elf` with base
  `0xffffffc008000000`; `llvm-nm` recovered 127674 symbol lines.
- A single valid raw BTF blob was found at Image interval
  `[0x1bf3ecc, 0x21c0cad)`; BTF SHA-256:
  `50c8e5540b03c99259ae401674916ae4a1fa211ee10b983463b9b2c0c207455d`.
- BTF-derived layouts include `task_struct` size `0x1200`, cred/real_cred
  `0x798/0x790`, `rt_mutex_waiter.task` `0x30`, `file_operations` size
  `0x120`, and `struct page` size `0x40`.
- Generated `src/targets/m35x-M356BXXU8DZE3/p0_fingerprint.h` from the exact
  Image at probe `0x1f0000`; independent readback verified 32 rows and 256
  qwords.
- Replaced the fail-closed scaffold header with a static M356B profile using
  exact ELF/BTF-derived values. No support-feed entry or proprietary artifact
  was published.

## Remaining blockers

- No Android NDK is installed, so the Makefile cannot build the ARM64 payload.
- P0 physical addresses, tracefs route/caller, and collision/page tuning still
  need bootloader/runtime evidence. The current header retains the closest
  documented profile defaults for those gates and must not be called hardware
  validated.
