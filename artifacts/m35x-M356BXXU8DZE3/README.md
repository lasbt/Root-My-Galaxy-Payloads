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
KASLR route (`APP_PHYS_P0_ORACLE`, the macro all other targets and the
P0_ORACLE_* constants expect; the previous header used the dead `PHYS_P0_ORACLE`
that no source reads) plus the MCAST stack writer, the closed fops/pipe gates,
and MCAST waiter offset `0x28`. First hardware test of this corrected route.
Verify its SHA-256 before copying it to a phone:

```text
7e76bb37ea26810ee35a02a9d12d0fe34c8b6623b6fe0e46da7dbd729fe44a1d
```

The previous MCAST + fingerprint-KASLR candidate (`cd6a760d…`) reached
`writer-enter` and rebooted three times; this candidate is the corrected route,
still unvalidated on hardware.

The root helper in this directory was built from the same target profile. It is
also unvalidated on hardware; keep both files from the same build together.

Root helper SHA-256:

```text
8b7e3285e99cbef5c958d5512105ef0471179484d7a8cb834d23cc882d259fd1
```