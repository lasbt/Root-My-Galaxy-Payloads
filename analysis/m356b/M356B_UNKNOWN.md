# M356B unknowns and recovery plan

## Blocking unknowns

All values required by `docs/PORTING.md` that depend on the exact kernel remain
unknown: Image header/text offset, `KIMAGE_TEXT_BASE`, P0 virtual/physical
addresses, direct-map and VMEMMAP boundaries, KASLR range, P0 fingerprint,
BTF/DWARF availability, every symbol offset, every structure layout, tracefs
event/caller, pselect shift, MCAST/FPSIMD route, SLAB/cache configuration,
SELinux/security internals, module config/vermagic/exports, and all payload-page
collision tuning.

This includes `MM_STRUCT_SZ`, `KMALLOC_CGROUP_TYPE`, `KMALLOC_CACHE_TYPES`,
credential offsets, fake task/waiter fields, `INIT_TASK_OFF`, credential
functions, UMH work/data, ashmem/configfs/pipe callbacks, netfilter logger and
boot-id locations, `FOPS_OFF`, `ROOT_UMH_*`, and `SLIDE_*` values.

## Required evidence to promote values

1. Exact AP/BL firmware for `M356BXXU8DZE3`, including `meta-data/fota.zip`.
2. Decompressed `boot.img`, kernel bytes, exact SHA-256, ARM64 Image header,
   and vendor boot/ramdisk metadata.
3. BL/sboot disassembly proving `P0_PHYS_OFFSET` and
   `P0_KERNEL_PHYS_LOAD`.
4. Recovered ELF, sorted symbols, disassembly, and validated raw BTF. Reject a
   BTF candidate unless the complete header, bounds, and string section pass.
5. Target config, source/patch information, `Module.symvers`, and exact
   KernelSU build metadata.
6. Derivation of trace event ID, worker caller, pselect word shift, route, and
   collision layout from M356B.
7. Generate `p0_fingerprint.h` from the M356B raw Image and independently
   verify every source qword.
8. Only after static review, perform separate non-destructive hardware
   validation. Hardware execution is not implied by this offline port.

## Promotion rule

A value may be marked `DERIVED` only when calculated from an exact M356B
artifact with the method in `docs/PORTING.md`. A value observed in A546E is
never promoted merely because the kernel release string matches.
