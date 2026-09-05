# M356B build report

## Changes built

The M356B app payload, release app payload, and root helper were built with
Android NDK r27d. The profile has not been hardware validated.

## Checks

### Direct preprocessor check

Command shape:

```sh
printf '#include "offset.h"\n' | clang -E -Isrc \
  -DTARGET_HEADER='"targets/m35x-M356BXXU8DZE3/target.h"' -x c -
```

Previous scaffold result: expected failure from `target.h`:

```text
M356BXXU8DZE3 target blocked: exact Image/ELF/BTF/config and P0 fingerprint are unavailable
```

The current target instead passes this preprocessor check and does not use
copied A546E offsets for the Image/ELF/BTF-derived values.

### Makefile check

Command attempted:

```sh
make TARGET=m35x-M356BXXU8DZE3 ANDROID_NDK_HOME=/nonexistent
```

Result: the container has no usable Android NDK; Make stopped while looking for
`/nonexistent/toolchains/llvm/prebuilt/linux-x86_64/bin/aarch64-linux-android35-clang`.
The direct clang check above separately validated the target guard.

### Artifact/build checks

- M356B app and root helper artifacts exist under
  `artifacts/m35x-M356BXXU8DZE3/`.
- Release app MCAST build: `104128` bytes,
  SHA-256 `fa9da64d3d87da70b4d69eae017ac5bc474fafd164865a729dde9be9c676f2e8`.
- Root helper: `26896` bytes,
  SHA-256 `8b7e3285e99cbef5c958d5512105ef0471179484d7a8cb834d23cc882d259fd1`.
- No M356B KernelSU `.ko` or `ksud` exists.
- M356B P0 fingerprint generated and read back: 32 rows, 256 qwords.
- `support/targets-v3.json` was intentionally not changed.
- No hardware or exploit execution was performed.
- No flashing, bootloader operation, or irreversible action was performed.

## First hardware test

The first pselect build reached `writer-enter` and rebooted. After recovery:
`sys.boot.reason=reboot`, `ro.boot.bootreason=reboot`, SELinux was `Enforcing`,
and ADB remained `uid=2000(shell)`. `/sys/fs/pstore` returned `Permission
denied`. The attached `last-kmsg` is empty and the attached `pstore` contains
only `===`; the post-reboot logcat has no pre-reboot kernel panic trace. The
run is recorded as a crash/failure, not a root result.

## MCAST rebuild

The target was rebuilt with `SLIDE_STACK_WRITER=1`, `MCAST_WAITER_OFF=0x78`,
the direct fops route, post-fops pipe order, exact pipe-buffer gate, and
right-only PI layout. The M356B ELF disassembly confirms the IPv6 source-request
copy begins at `do_ipv6_setsockopt` stack offset `0x40`; the resulting MCAST
waiter chain remains statically derived and still needs one controlled device
validation. The previous pselect artifact must not be retested.

## Remaining build prerequisites

Audit the exact KernelSU module vermagic/exports, collect runtime evidence, and
validate the artifacts on the exact device before publishing a support-feed
entry.
