# M356BXXU8DZE3 verification

## Escopo

Esta verificação cobre o inventário local e a decisão de não criar um target
M356B. Não houve execução de exploit nem interação destrutiva com dispositivo.

## Parameter matrix

| Parameter | Value | Source | Verification | Confidence |
| --- | --- | --- | --- | --- |
| model | `SM-M356B` | solicitação do usuário | sem firmware local | LOW / unverified |
| firmware | `M356BXXU8DZE3` | solicitação do usuário | sem AP/FUS metadata local | LOW / unverified |
| CSC | `ZTO` | solicitação do usuário | sem properties local | LOW / unverified |
| SoC | Exynos 1380 / `s5e8835` | solicitação do usuário | sem DTB/boot local | LOW / unverified |
| kernel release | `5.15.189-android13-3-33470412` | solicitação do usuário | sem `uname`/ELF/config local | LOW / unverified |
| architecture | `UNKNOWN` | deveria vir do Image/ELF | não conferido no binário | NONE |
| kernel base | `UNKNOWN` | deveria vir do ELF/Image | nenhum ELF M356B | NONE |
| KASLR slide/range | `UNKNOWN` | tracefs/P0/dispositivo | nenhum runtime ou Image | NONE |
| `P0_PHYS_OFFSET` | `UNKNOWN` | BL/sboot/disassembly | nenhum BL/sboot | NONE |
| `P0_KERNEL_PHYS_LOAD` | `UNKNOWN` | BL/sboot + Image header | nenhum BL/sboot/Image | NONE |
| BTF presence/integrity | `UNKNOWN` | BTF raw/ELF | nenhum BTF | NONE |
| symbols | `UNKNOWN` | `vmlinux.nm`/ELF | nenhum ELF/symbol dump | NONE |
| `task_struct`/`cred` | `UNKNOWN` | BTF + disassembly | nenhum BTF/disassembly | NONE |
| `mm_struct` | `UNKNOWN` | BTF | nenhum BTF | NONE |
| `file`/`file_operations` | `UNKNOWN` | BTF | nenhum BTF | NONE |
| `struct page` | `UNKNOWN` | BTF + source | nenhum BTF/source | NONE |
| `struct sk_buff` | `UNKNOWN` | BTF + disassembly | nenhum BTF/disassembly | NONE |
| `rt_mutex_waiter` | `UNKNOWN` | BTF + disassembly | nenhum BTF/disassembly | NONE |
| ashmem/configfs/pipe layouts | `UNKNOWN` | BTF + symbols | nenhum artefato M356B | NONE |
| tracefs event/callers | `UNKNOWN` | config/ELF/runtime | nenhum config/ELF/runtime | NONE |
| MCAST waiter/frame | `UNKNOWN` | disassembly exato | nenhum disassembly | NONE |
| SLAB/kmalloc | `UNKNOWN` | config/BTF/symbols | nenhum config/BTF | NONE |
| KDP/RKP/DEFEX | `UNKNOWN` | source/config/disassembly | nenhum source/config | NONE |
| SELinux internals | `UNKNOWN` | ELF/BTF/runtime controlado | nenhum artefato | NONE |
| KernelSU vermagic/exports | `UNKNOWN` | config/ELF/Module.symvers | nenhum arquivo correspondente | NONE |
| P0 fingerprint | `NOT GENERATED` | raw Image M356B | Image ausente | NONE |
| target.h | `NOT CREATED` | requer parâmetros anteriores | criação bloquearia validação | CONFIRMED decision |

## Cross-checks performed

- Busca por nomes/conteúdo `M356B`, `M356BXXU8DZE3` e `DZE3`: nenhuma
  ocorrência local.
- Busca de imagens, ELF, BTF, configs, dumps e source específicos: nenhum
  artefato M356B.
- Comparação dos documentos 5.15.189 existentes: todos os principais são
  `android13-8`, não `android13-3`.
- Conferência de divergência de layout: `task_struct.cred` aparece como
  `0x5e0` em `S9180` e `0x798` em `S918B/X710`; `file_operations` aparece como
  `0xe8` e `0x120` em referências distintas.
- Conferência metodológica em `docs/PORTING.md`: exige BTF/ELF/disassembly,
  prova física e fingerprint P0 do target exato.

## Verification blockers

Para transformar qualquer linha `UNKNOWN` em valor válido, anexar os artefatos
M356B exatos e repetir a matriz. Um valor só deve ser promovido quando houver
concordância entre BTF, símbolo/disassembly e, quando aplicável, source/config;
valores copiados de `dm2q`, `dm3q` ou `gts9` não satisfazem essa condição.