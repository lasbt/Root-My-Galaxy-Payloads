# SM-M356B M356BXXU8DZE3 artifact

This artifact was built from the statically derived M356B target profile with
Android NDK r27d. It has not been validated on physical hardware and is not
listed in `support/targets-v3.json`.

Build command:

```sh
ANDROID_NDK_HOME=/tmp/android-ndk/android-ndk-r27d \
  make release TARGET=m35x-M356BXXU8DZE3 API=35 \
  OUTDIR=/tmp/m356b-build-release
```

The app payload is exactly 104128 bytes. This build uses the physical-P0-oracle
KASLR route (`APP_PHYS_P0_ORACLE`, matching every other target; the previous
header used the dead `PHYS_P0_ORACLE` macro that no source reads) plus the MCAST
stack writer, the closed fops/pipe gates, and `MCAST_WAITER_OFF=0x80`
(re-derived from the M356B vmlinux.elf; the earlier `0x28` under-counted the
stack frames). Verify its SHA-256 before copying it to a phone:

```text
1395dfd7316f999c558b7f1017098538b34e3c528ed060fa1137560e98292a46
```

History: the MCAST + fingerprint-KASLR route (`0x28`) and the corrected
physical-P0-oracle route (`0x28`) both reached `writer-enter` and rebooted
(4+ hardware runs). This candidate keeps the corrected route and changes only
`MCAST_WAITER_OFF` to a re-derived `0x80`. Still unvalidated on hardware.

The root helper in this directory was built from the same target profile. It is
also unvalidated on hardware; keep both files from the same build together.

Root helper SHA-256:

```text
8b7e3285e99cbef5c958d5512105ef0471179484d7a8cb834d23cc882d259fd1
```