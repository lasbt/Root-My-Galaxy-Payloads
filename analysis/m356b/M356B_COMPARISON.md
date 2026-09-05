# M356B comparison matrix

`M356B` is marked `UNKNOWN` unless the value is directly supplied as identity
metadata. `A546E` values below are quoted only as PR evidence and are not
proposed M356B values.

| VALUE | A546E | OUTROS TARGETS | M356B | BASIS | STATUS |
|---|---|---|---|---|---|
| `KIMAGE_TEXT_BASE` | `0xffffffc008000000` | common in 5.15 refs, not universal | UNKNOWN | exact ELF/Image | UNKNOWN |
| `P0_PAGE_OFFSET` | `0xffffff8000000000` | same in 5.15 refs | UNKNOWN | exact MM layout | UNKNOWN |
| `P0_PHYS_OFFSET` | `0x80000000` | often same, not portable | UNKNOWN | BL/sboot | UNKNOWN |
| `P0_KERNEL_PHYS_LOAD` | `0x80000000` | differs across targets | UNKNOWN | BL/sboot + Image | UNKNOWN |
| `DIRECT_MAP_BASE` | `0xffffff8000000000` | same in cited 5.15 refs | UNKNOWN | target MM config | UNKNOWN |
| `DIRECT_MAP_END` | `0xffffff9000000000` | same in cited 5.15 refs | UNKNOWN | target MM config | UNKNOWN |
| `VMEMMAP_START` | `0xfffffffe00000000` | same in cited 5.15 refs | UNKNOWN | target MM config | UNKNOWN |
| `KERNELSNITCH_IDENTITY_END` | `0xffffff9000000000` | same in cited 5.15 refs | UNKNOWN | target MM config | UNKNOWN |
| `MM_STRUCT_SZ` | `0x400` | varies; legacy refs differ | UNKNOWN | BTF | UNKNOWN |
| `KMALLOC_CGROUP_TYPE` | `1` | target/config dependent | UNKNOWN | config + code | UNKNOWN |
| `KMALLOC_CACHE_TYPES` | `3` | target/config dependent | UNKNOWN | config + code | UNKNOWN |
| `TASK_STRUCT_CRED_OFF` | `0x798` | S9180 differs (`0x5e0`) | UNKNOWN | BTF | UNKNOWN |
| `TASK_STRUCT_REAL_CRED_OFF` | `0x790` | S9180 differs (`0x5d8`) | UNKNOWN | BTF | UNKNOWN |
| `FAKE_TASK_TASK_GROUP_OFF` | `0x400` | target-specific | UNKNOWN | BTF/disassembly | UNKNOWN |
| `INIT_TASK_OFF` | `0x0237fd80` | differs | UNKNOWN | ELF symbol | UNKNOWN |
| `PREPARE_KERNEL_CRED_OFF` | `0x0011367c` | differs | UNKNOWN | ELF symbol | UNKNOWN |
| `COMMIT_CREDS_OFF` | `0x00112f24` | differs | UNKNOWN | ELF symbol | UNKNOWN |
| `OVERRIDE_CREDS_OFF` | `0x0011330c` | differs | UNKNOWN | ELF symbol | UNKNOWN |
| `ROOT_TASK_GROUP_OFF` | `0x02571f40` | differs | UNKNOWN | ELF symbol | UNKNOWN |
| `SELINUX_ENFORCING_OFF` | `0x026452a8` | differs | UNKNOWN | ELF/BTF | UNKNOWN |
| `KMALLOC_CACHES_OFF` | `0x01ae8db0` | A546E revisions differ by `0x180` | UNKNOWN | ELF symbol | UNKNOWN |
| `ANON_PIPE_BUF_OPS_OFF` | `0x018f6fa0` | A546E revisions differ by `0x180` | UNKNOWN | ELF symbol | UNKNOWN |
| `SYSTEM_UNBOUND_WQ_OFF` | `0x0236ae20` | can be common only after proof | UNKNOWN | ELF symbol | UNKNOWN |
| `CALL_USERMODEHELPER_EXEC_WORK_OFF` | `0x00100ae0` | branch/build dependent | UNKNOWN | ELF symbol | UNKNOWN |
| `ASHMEM_FOPS_OFF` | `0x01a97bb0` | A546E revisions differ | UNKNOWN | ELF symbol | UNKNOWN |
| `ASHMEM_MISC_FOPS_OFF` | `0x024df810` | target-specific | UNKNOWN | symbol + BTF | UNKNOWN |
| ashmem callbacks | `0x00ce7170` etc. | can change by build | UNKNOWN | ELF symbols | UNKNOWN |
| configfs callbacks | `0x00475598`, `0x00475a54` | target-specific | UNKNOWN | ELF symbols | UNKNOWN |
| `COPY_SPLICE_READ_OFF` | `0x003fc730` | target-specific | UNKNOWN | ELF symbol | UNKNOWN |
| `NOOP_LLSEEK_OFF` | `0x003b1918` | target-specific | UNKNOWN | ELF symbol | UNKNOWN |
| `SLIDE_NFULNL_LOGGER_NAME_OFF` | `0x017de8b8` | differs between A546E revisions | UNKNOWN | string/symbol cross-check | UNKNOWN |
| `SLIDE_NFULNL_LOGGER_OBJECT_OFF` | `0x023725a0` | target-specific | UNKNOWN | ELF symbol | UNKNOWN |
| `SLIDE_TRACEFS_EVENT_ID` | `108` | `dm3q/gts9` also `108`, not proof | UNKNOWN | trace enum/link order/runtime | UNKNOWN |
| `SLIDE_TRACEFS_WORKER_CALLER_OFF` | `0x0010825c` | differs | UNKNOWN | disassembly | UNKNOWN |
| `SLIDE_PSELECT_WORD_SHIFT` | not exposed by PR target | varies by stack frame | UNKNOWN | pselect disassembly | UNKNOWN |
| `FOPS_OFF` | `0x7000` after PR fix | route/page-layout choice | UNKNOWN | collision validation | UNKNOWN |
| `ROOT_UMH_WORK_OFF` | `0x7800` after PR fix | route/page-layout choice | UNKNOWN | collision validation | UNKNOWN |
| `ROOT_UMH_DATA_OFF` | `0x7a00` after PR fix | route/page-layout choice | UNKNOWN | collision validation | UNKNOWN |
| `SLIDE_ROUTE` | `FPSIMD` after PR | MCAST/FPSIMD varies | UNKNOWN | runtime route test | UNKNOWN |
| P0 fingerprint | 125 entries, 0x4000 step | each Image differs | UNKNOWN | raw exact Image | UNKNOWN |
| KASLR candidate range | A546E profile range | target-specific | UNKNOWN | Image/runtime | UNKNOWN |
| `STRUCT_PAGE`/`file_operations` layouts | BTF-confirmed A546E | differ across refs | UNKNOWN | BTF raw | UNKNOWN |
| configfs/workqueue/pipe layouts | A546E BTF/profile | target-specific | UNKNOWN | BTF + code | UNKNOWN |
| KernelSU vermagic/exports | A546E asset reuse claim | exact config required | UNKNOWN | config/module audit | UNKNOWN |

## What is reusable

The generic exploit source, target-header architecture, P0 generation method,
configfs safety gate, and documentation method are reusable. The A546E route
choice and numeric data are not automatically reusable. Existing payloads and
`.ko`/`ksud` files are not M356B artifacts.
