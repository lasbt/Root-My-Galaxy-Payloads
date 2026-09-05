# M356B final report

## 1. Firmware found

Samsung FUS confirmed:
`M356BXXU8DZE3/M356BOWO8DZE3/M356BXXU8DZE3/M356BXXU8DZE3` for model
`SM-M356B`, region `ZTO`. The public index entry is:
https://www.sammobile.com/samsung/galaxy-m35/firmware/SM-M356B/ZTO/download/M356BXXU8DZE3/2000586/

The official FUS package was downloaded outside git to `/tmp`. Size:
`9016478636` bytes. SHA-256:
`01d5467f794259f5e2ac468cacdfcd5d27e13ba175fb064c192a3197eeaad63a`.

## 2. Kernel found

The AP contains `boot.img.lz4`, `init_boot.img.lz4`, `vendor_boot.img.lz4`,
`dtbo.img.lz4` and `meta-data/fota.zip`. The decompressed boot image is an
Android boot image with a 39225856-byte ARM64 Image. FOTA `BOOT/kernel` is
byte-identical to the kernel extracted from boot.

Kernel SHA-256:
`0dbbdf35e8dd7caddb40365fb53a8c606fb084b0165a454ef4ed14c4ac0813b3`.
Release string:
`5.15.189-android13-3-33470412`.
Fingerprint:
`samsung/m35xxx/essi:16/BP4A.251205.006/M356BXXU8DZE3:user/test-keys`.

## 3. Sources and tools

Sources used: Samsung FUS, the public SamMobile index, repository
`zainarbani/Root-My-Galaxy-Payloads` PR #3, and local `docs/PORTING.md`.
Tools used: `samloader-rs` 2.0.0, `lz4`, `vmlinux-to-elf`, `llvm-nm`,
`readelf`, `strings`, a strict raw-BTF parser, and
`tools/generate_p0_fingerprint.pl`.

## 4. Artifacts found and reconstructed

Found: official firmware package, boot images, FOTA kernel, raw BTF, and
symbolized ELF reconstructed from the exact Image. No M356B payload `.so`,
KernelSU `.ko`, or `ksud` was found or copied from another target.

Reconstructed: `p0_fingerprint.h` with 32 candidates and 256 verified source
qwords, and a real static target header at
`src/targets/m35x-M356BXXU8DZE3/target.h`.

## 5. Verified values

- Model `SM-M356B`, device `m35x`, board `s5e8835`.
- Android fingerprint and kernel release above.
- `KIMAGE_TEXT_BASE = 0xffffffc008000000`.
- ARM64 Image `text_offset = 0`, `image_size = 0x2820000`, flags `0xa`.
- `task_struct` size `0x1200`; `cred/real_cred = 0x798/0x790`.
- `file_operations` size `0x120`; ioctl/mmap/open/release/splice/show offsets
  are `0x50/0x60/0x70/0x80/0xc8/0xe0`.
- `struct page` size `0x40`, compound head `0x08`.
- `rt_mutex_waiter.task = 0x30`, lock `0x38`, wake state `0x40`, prio
  `0x44`, deadline `0x48`, and ww context `0x50`.
- ELF symbol offsets were derived for all critical symbols; notably M356B
  differs from A546E at `kmalloc_caches`, `anon_pipe_buf_ops`, `ashmem_fops`,
  `ashmem` callbacks, and the netfilter string.

## 6. Derived values

The complete critical symbol set was derived from the recovered ELF, including
credentials, init task, SELinux, workqueue, configfs, ashmem, pipe, netfilter,
random table and boot-id symbols. The BTF interval and hash are recorded above.
The P0 fingerprint was generated and independently read back from the exact
Image.

## 7. Inferred or not independently runtime-verified

`P0_PHYS_OFFSET`, `P0_KERNEL_PHYS_LOAD`, virtual map constants, FPSIMD route,
tracefs event/caller, KASLR candidate range, and scratch collision tuning still
need bootloader or device-runtime evidence. Their current header values are
documented profile defaults, not a hardware-validation claim.

## 8. A546E comparison

PR #3 commit `cafc6bd` proves A546E uses the same kernel release/build number
and the same text base and several symbol offsets. It also records A546E-only
changes: `kmalloc_caches`, `anon_pipe_buf_ops`, `ashmem_fops`, netfilter
string, task layouts and `rt_mutex_waiter.task` differences across revisions.
Commits `50de045` and `94ecec9` change safety probing and route selection;
`98ccbf7` changes page collision addresses; `026a357` replaces binaries; and
`87b9399` changes feed URLs. Only generic source and methodology are reusable.

## 9. Implementation and build

The scaffold was replaced by a target header and exact P0 fingerprint. The
header passes the host preprocessor and editor diagnostics. The target was
built successfully with Android NDK r27d. After the pselect crash, the app was
rebuilt with the MCAST writer and closed fops/pipe gates. The release app is
`104128` bytes and the root helper is `26896` bytes; both are in
`artifacts/m35x-M356BXXU8DZE3/`.

## 10. Validation and remaining problem

Static validation and offline build passed for firmware identity, kernel equality, Image header,
ELF architecture/base, symbol recovery, BTF integrity, layouts, fingerprint
readback, target preprocessing and macro integration. The port is therefore
implemented as a statically derived profile, but remains **not hardware
validated** and is not published in `support/targets-v3.json`.

The first physical test on the exact SM-M356B reached `writer-enter` and then
the device rebooted. After recovery, ADB reported `sys.boot.reason=reboot` and
`ro.boot.bootreason=reboot`; SELinux remained `Enforcing` and the shell stayed
`uid=2000`, so no root result was obtained. `/sys/fs/pstore` was inaccessible
from the ADB shell. The supplied post-reboot diagnostics add no panic trace:
`last-kmsg` is empty, `pstore` contains only `===`, and `logcat` begins during
the next boot. The device remained `ro.boot.verifiedbootstate=green`,
`ro.boot.flash.locked=1`, and `ro.debuggable=0`. This is a hardware crash or
forced reboot during the writer stage, not a successful port.

The rebuilt MCAST artifact was then tested once. It reported
`stack_writer=mcast`, `reclaim=legacy`, `fops=direct`, and `pipe=after-fops`,
reached the same `writer-enter` stage, and rebooted again. Therefore changing
the writer from pselect to MCAST did not establish a safe runtime chain. The
The M356B profile is not hardware validated and must not be retested without
the new synchronization candidate, whose consumer is armed before MCAST
`setsockopt()`, and further static evidence for the MCAST/PI and reclaim stages.

The exact remaining blockers are validation of the rebuilt MCAST artifact with
the corrected M356B-specific waiter offset `0x28` and
runtime-dependent reclaim parameters. The previous pselect artifact must not
be retested. No flashing, unlocking, or
persistent modification was performed.