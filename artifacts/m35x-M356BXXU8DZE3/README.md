# SM-M356B M356BXXU8DZE3 artifact

This artifact is built from an **inferred** M356B target profile. All
kernel-dependent values are inferred from peer 5.15.189 targets
(dm3q-S918BXXSAFZF5 and gts9u-X916BXXS6EZG3), NOT derived from the M356B
kernel image. The workspace contains no M356B firmware or kernel components.

Build command (requires Android NDK r27d):

```sh
ANDROID_NDK_HOME=/path/to/android-ndk-r27d \
  make release TARGET=m35x-M356BXXU8DZE3 API=35 \
  OUTDIR=/tmp/m356b-build-release
```

The app payload is exactly 104128 bytes. Build configuration:

```text
stack_writer=mcast reclaim=legacy fops=direct pipe=after-fops
```

Key values (inferred, NOT verified on M356B hardware):

| Value | Current | Previous | Rationale |
|-------|---------|----------|----------|
| MCAST_WAITER_OFF | 0x78 | 0x80 | Both verified 5.15.189 peers (dm3q, gts9u) use 0x78 |
| P0_KERNEL_PHYS_LOAD | 0x80080000 | 0x80000000 | 0x80000000 = P0_PHYS_OFFSET (zeroes delta, breaks oracle); 0x80080000 matches gts9u |
| MM_ORDER | 3 | (default) | Explicit, matches dm3q and gts9u |
| MM_STRUCT_SZ | 0x400 | 0x400 | Matches dm3q (Exynos, 5.15.189); gts9u uses 0x3e0 |

Known uncertainties requiring M356B kernel analysis:
- P0_KERNEL_PHYS_LOAD: must be confirmed against M356B boot image
- MM_STRUCT_SZ: may need 0x3e0 if M356B matches gts9u layout
- All symbol offsets: inferred from A546E PR reference, not derived from M356B

Verify the SHA-256 before copying to a phone. The hash below is from the
**previous** build (0x80/0x80000000) and will change after rebuild:

```text
1395dfd7316f999c558b7f1017098538b34e3c528ed060fa1137560e98292a46
```

History:
- 0x28 (original): 4+ runs, all rebooted at writer-enter
- 0x80 (re-derived): same symptom, 4+ runs
- 0x78 (current, peer-verified): NOT YET TESTED on M356B hardware

The root helper in this directory was built from the same target profile. It is
also unvalidated on hardware; keep both files from the same build together.

Root helper SHA-256 (previous build):

```text
8b7e3285e99cbef5c958d5512105ef0471179484d7a8cb834d23cc882d259fd1
```
