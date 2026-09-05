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
- Release app MCAST build with M356B waiter offset `0x28`: `104128` bytes,
  SHA-256 `dbd508744c355a2733b0be5d2097fa1e4a53353e704f8f28fe46b522fa31fd8b`.
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

## Second hardware test

The rebuilt MCAST artifact reached `stack_writer=mcast`, then the same
`writer-enter` stage, and rebooted. It produced no root result. This rules out
the pselect route as the sole cause and leaves the MCAST/PI or reclaim/page
preparation chain unvalidated. No further device attempts are authorized by
this report.

## MCAST rebuild

The target was rebuilt with `SLIDE_STACK_WRITER=1`, `MCAST_WAITER_OFF=0x28`,
the direct fops route, post-fops pipe order, exact pipe-buffer gate, and
right-only PI layout. The M356B ELF disassembly confirms the IPv6 source-request
copy begins at `do_ipv6_setsockopt` stack offset `0x40`; the resulting MCAST
waiter chain remains statically derived and still needs one controlled device
validation. The attempted `SLIDE_WAITER_WAKE_STATE`/`SLIDE_LOCK_OWNER_VALUE`
adjustment was removed because `put_fake_waiter()` writes zero directly for
the compact waiter and the MCAST binary hash did not change. The previous
pselect artifact must not be retested.

The M356B-specific MCAST derivation found that the absolute waiter position is
`E - 0x318`: syscall frames `0x10 + 0x30 + 0x20 + 0x70 + 0x130 + 0x1b0`
minus `futex_wait_requeue_pi` waiter position `0x98`. The MCAST buffer position
is `E - 0x340`: frames `0x10 + 0x60 + 0x10 + 0x40 + 0x2c0` minus
`do_ipv6_setsockopt` buffer position `0x40`. Therefore the relative offset is
`(E - 0x318) - (E - 0x340) = 0x28`. The earlier `0x88` adjustment was wrong
and must not be tested.

The MCAST synchronization was then corrected: the consumer is armed before
`setsockopt()` copies the attacker-controlled stack stamp. Previously it was
armed after the kernel call, leaving the writer window unsynchronized. The
resulting release app hash is
`cd6a760dc09af2ed643a60d9d3ca71d55d8a543ff1bff7711933e9a473e5bd5c`.

## Remaining build prerequisites

Audit the exact KernelSU module vermagic/exports, collect runtime evidence, and
validate the artifacts on the exact device before publishing a support-feed
entry.
