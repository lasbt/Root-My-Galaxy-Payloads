# SM-M356B M356BXXU8DZE3

Status da análise: **inconclusiva por falta dos artefatos do alvo**. Este
relatório foi produzido somente a partir do checkout local e dos metadados do
alvo fornecidos na solicitação. Nenhum offset, endereço, layout ou parâmetro
do M356B foi inferido por cópia de outro dispositivo.

## Kernel identity

| Campo | M356B informado | Evidência local | Resultado |
| --- | --- | --- | --- |
| Device/model | `SM-M356B` | solicitação do usuário | informado, não verificado |
| SoC/platform | Exynos 1380 / `s5e8835` | solicitação do usuário | informado, não verificado |
| Region/CSC | `ZTO` | solicitação do usuário | informado, não verificado |
| Firmware/build | `M356BXXU8DZE3` | solicitação do usuário | informado, não verificado |
| Android / One UI | 16 / 8.5 | solicitação do usuário | informado, não verificado |
| Kernel release | `5.15.189-android13-3-33470412` | solicitação do usuário | informado, não verificado |
| Architecture | ARM64 presumida pelo SoC e pelo padrão dos targets | sem `Image`/ELF local | UNKNOWN |
| Build fingerprint | `M356BXXU8DZE3` foi fornecido como versão, não como fingerprint Android completo | sem `meta-data/fota.zip`/properties | UNKNOWN |

O kernel não pode ser considerado equivalente aos targets existentes apenas por
compartilhar `5.15.189`: os targets disponíveis são `android13-8`, com builds
`33413713` ou `33413632`; o alvo informado é `android13-3-33470412`, além de
usar uma plataforma Exynos diferente dos targets 5.15.189 Qualcomm/Kalama.

## Available artifacts

O inventário foi feito no checkout em `/workspaces/Root-My-Galaxy-Payloads`.

### Presentes

- Headers e fingerprints P0 dos targets já publicados em `src/targets/`.
- Documentação de portabilidade em `docs/PORTING.md`.
- Documentação e headers dos targets 5.15.189 `dm2q`, `dm3q` e `gts9`.
- Payloads `.so`, helpers root, módulos `.ko` e loaders `ksud` de targets
  existentes.
- Scripts auxiliares, incluindo geração de fingerprint P0 e auditoria de
  módulos.
- Nenhum arquivo com nome ou conteúdo identificando `M356B`, `M356BXXU8DZE3`
  ou `DZE3`.

### Ausentes para o M356B

Não existem no workspace os seguintes artefatos do firmware exato:

| Artefato | Estado | Consequência |
| --- | --- | --- |
| `boot.img` / `boot.img.lz4` | AUSENTE | não há kernel bruto nem hash para verificar |
| `vendor_boot.img` / comprimido | AUSENTE | não há ramdisk/vendor metadata |
| `kernel/Image` | AUSENTE | não é possível validar ARM64, header ou texto |
| `vmlinux.elf` | AUSENTE | não há símbolos/disassembly confiáveis |
| `vmlinux.btf`/BTF bruto | AUSENTE | não há layouts estruturais |
| kernel config / `Module.symvers` | AUSENTE | não há config, vermagic ou ABI de módulos |
| dumps, `kallsyms`, DTB, BL/sboot | AUSENTE | não há prova de carga física/P0 |
| fonte Samsung correspondente | AUSENTE | não há comparação de patches/configuração |
| dumps de tracefs/SELinux/configfs | AUSENTE | não há validação de runtime |

Os `.ko` e `ksud` presentes pertencem a outros modelos/builds e não são
evidência para o M356B.

## BTF analysis

M356B: **UNKNOWN**. Não há BTF para validar o cabeçalho, limites das seções,
tipos, tamanhos ou `bits_offset`. A metodologia do projeto exige validar o BTF
completo e usar o dump raw para números, não copiar a declaração C de outro
kernel.

Target de referência: os documentos de `S9180`, `S918B` e `X710` descrevem BTF
recuperado externamente, mas isso somente prova os respectivos kernels.

Resultado: **UNKNOWN / NÃO COMPARÁVEL**. Evidência necessária: `Image` ou
`vmlinux.elf` exato, BTF embutido validado ou um `vmlinux.btf` independente,
mais `bpftool btf dump format raw` preservado.

## Symbol analysis

Todos os símbolos necessários do M356B são **UNKNOWN**, incluindo:

- `init_task`, `prepare_kernel_cred`, `commit_creds`, `override_creds`;
- `root_task_group`, `selinux_state`, `system_unbound_wq`;
- `call_usermodehelper_exec_work`, `kmalloc_caches`;
- `ashmem_fops`, `ashmem_misc`, `ashmem_ioctl`, `ashmem_mmap` e demais
  callbacks ashmem;
- `configfs_read_iter`, `configfs_bin_write_iter`, `copy_splice_read`,
  `noop_llseek`, `anon_pipe_buf_ops`;
- `nfulnl_logger`, a string `nfnetlink_log`, `loggers` e a entrada
  `random_table[]` de `boot_id`.

Nenhum endereço estático, relativo à base, dependente de slide ou físico do
M356B foi recuperado. Os offsets dos headers existentes são somente
referências e não podem ser usados como valores M356B.

## Structure layouts

| Estrutura/uso | M356B offset/tamanho | Referência documentada | Resultado |
| --- | --- | --- | --- |
| `task_struct` | UNKNOWN | tamanho/offsets variam entre refs | UNKNOWN |
| `task_struct.cred` / `real_cred` | UNKNOWN | S9180: `0x5e0`/`0x5d8`; S918B/X710: `0x798`/`0x790` | DIFFERENT entre refs; M356B UNKNOWN |
| `cred` | UNKNOWN | nenhuma prova M356B | UNKNOWN |
| `mm_struct` | UNKNOWN | X710 documenta `0x3e0`; header usa `MM_STRUCT_SZ 0x400` | UNKNOWN |
| `file` | UNKNOWN | nenhuma prova M356B | UNKNOWN |
| `file_operations` | UNKNOWN | S918B: `0x120`; S9180: `0xe8` | DIFFERENT entre refs; M356B UNKNOWN |
| `struct page` | UNKNOWN | refs documentam `0x40` | UNKNOWN |
| `struct sk_buff` | UNKNOWN | `SKB_DATA_DELTA`/tamanho são target-specific | UNKNOWN |
| `rt_mutex_waiter` | UNKNOWN | refs usam waiter compacto, mas campos precisam disassembly/BTF | UNKNOWN |
| estruturas ashmem | UNKNOWN | funções/fops variam por kernel | UNKNOWN |
| configfs | UNKNOWN | callbacks e layouts precisam ELF/BTF | UNKNOWN |
| pipe / `pipe_buf_operations` | UNKNOWN | `anon_pipe_buf_ops` e layout precisam símbolos/BTF | UNKNOWN |
| tracefs | UNKNOWN | ID e callers são derivados do kernel exato | UNKNOWN |
| MCAST/networking | UNKNOWN | `MCAST_WAITER_OFF` é derivado de frame/disassembly | UNKNOWN |
| SLAB/kmalloc | UNKNOWN | caches/configuração precisam config, BTF e símbolos | UNKNOWN |

Os valores de referência acima não são candidatos M356B; a divergência entre
eles é justamente uma demonstração de que o número da versão não basta.

## Security configuration

| Área | M356B | Evidência necessária | Resultado |
| --- | --- | --- | --- |
| KDP/RKP/DEFEX ou equivalente | UNKNOWN | config, source Samsung, símbolos e disassembly | UNKNOWN |
| `CONFIG_MODVERSIONS` / módulos | UNKNOWN | `.config`, vermagic, `Module.symvers` | UNKNOWN |
| `CONFIG_TRIM_UNUSED_KSYMS` | UNKNOWN | `.config`/ELF | UNKNOWN |
| SELinux internals/enforcing | UNKNOWN | ELF/BTF e, para validação, dump/runtime controlado | UNKNOWN |
| compiler/ABI | UNKNOWN | release metadata, ELF notes e source build | UNKNOWN |
| patches Samsung | UNKNOWN | source correspondente e diff/config exatos | UNKNOWN |

Não é válido reutilizar automaticamente o patch KernelSU KDP/RKP/DEFEX ou um
loader existente: os módulos disponíveis foram construídos para outros
vermagic, símbolos e configurações.

## Comparison with existing 5.15.189 targets

| Referência | Kernel | Plataforma | Evidência reportada | Limite da comparação |
| --- | --- | --- | --- | --- |
| `dm2q-S916BXXSAFZG1` | `5.15.189-android13-8-33413713` | Snapdragon/Kalama | hardware verified; MCAST, tracefs, SKB, ashmem, configfs, pipe | build branch e SoC diferentes |
| `dm3q-S9180ZHS8FZF5` | `5.15.189-android13-8-33413713` | Snapdragon/Kalama | BTF/ELF e port record | não é `android13-3`, nem Exynos |
| `dm3q-S918BXXSAFZF5` | `5.15.189-android13-8-33413713` | Snapdragon/Kalama | hardware end-to-end; `.text` compartilhado com S916B | dados, carga física e P0 continuam específicos |
| `gts9-X710XXS6EZF1` | `5.15.189-android13-8-33413632` | Snapdragon/Kalama | BTF/ELF e hardware | build diferente e SoC diferente |
| M356B | `5.15.189-android13-3-33470412` informado | Exynos 1380 | nenhum artefato local | sem base para equivalência |

A referência estrutural mais próxima da cadeia é `dm2q-S916B`, reforçada por
`dm3q-S918B`, porque ambas documentam a cadeia MCAST de 5.15.189. Isso é
proximidade metodológica, não compatibilidade demonstrada.

## Exploit-chain compatibility

| Estágio | Classificação | Justificativa |
| --- | --- | --- |
| initial primitive | UNKNOWN | implementação/patch do kernel M356B não disponível |
| heap/object manipulation | UNKNOWN | SLAB, caches e tamanhos de objetos não disponíveis |
| information leak | UNKNOWN | objetos/callbacks e caminhos netfilter não verificados |
| KASLR discovery | UNKNOWN | tracefs, evento, caller e alternativa P0 não verificados |
| arbitrary read/write | UNKNOWN | ashmem/configfs/pipe e page layout não verificados |
| kernel privilege transition | UNKNOWN | símbolos de cred/UMH e offsets não disponíveis |
| SELinux/security handling | UNKNOWN | `selinux_state` e hardening não disponíveis |
| KernelSU late-load/bootstrap | UNKNOWN | vermagic, exports, CRCs, KDP/RKP/DEFEX e ABI desconhecidos |

Não há incompatibilidade estrutural comprovada no M356B porque não há kernel
para testar a premissa. Também não há base para declarar a cadeia funcional.
O ponto de parada reprodutível é antes da análise da primitive inicial.

## Required target parameters

Antes de criar um target, seriam necessários, no mínimo:

1. identidade extraída do firmware e hash de `boot.img`, `vendor_boot.img` e
   kernel;
2. ARM64 Image header, base de imagem e `text_offset`;
3. prova de `P0_PHYS_OFFSET`/`P0_KERNEL_PHYS_LOAD` a partir de BL/sboot ou
   evidência equivalente;
4. ELF simbolizado, `vmlinux.nm`, disassembly e BTF validado;
5. layouts raw de todas as estruturas usadas pelo payload;
6. offsets de todos os símbolos/callbacks listados acima;
7. ID de tracefs e callers derivados do M356B;
8. config, source/patches Samsung e dados de módulo para KernelSU;
9. fingerprint P0 gerado do Image M356B, nunca de outro target;
10. verificações estáticas cruzadas e, separadamente, validação não destrutiva
    controlada no dispositivo.

## Parameters successfully recovered

Nenhum parâmetro técnico do M356B foi recuperado. A identidade acima é apenas
o conjunto de dados fornecido pelo usuário e está marcada como não verificada.

## Parameters still unknown

Todos os parâmetros dependentes do kernel permanecem desconhecidos: base
virtual, KASLR, carga física, BTF, símbolos, layouts, config, segurança,
tracefs, MCAST, SLAB, offsets de funções, P0 candidates/fingerprint, ABI de
KernelSU e qualquer endereço físico ou virtual derivado.

## Confidence for each parameter

| Parâmetro | Confiança | Razão |
| --- | --- | --- |
| modelo/build/SoC/CSC/kernel informados | baixa, não verificada | somente solicitação do usuário |
| ARM64 | baixa | inferência da plataforma, sem Image |
| demais parâmetros | nenhuma | não há artefato M356B correspondente |

## Portability assessment

**INCONCLUSIVA.** A cadeia 5.15.189 de `dm2q-S916B`/`dm3q-S918B` é uma
referência conceitual útil, mas não pode ser portada estaticamente para o
M356B com os arquivos atuais. `android13-3` versus `android13-8`, build
`33470412` versus `33413713`, Exynos 1380 versus Snapdragon/Kalama, patches
Samsung, configuração de segurança e layouts podem alterar todos os pontos
críticos.

Nenhum `src/targets/m356b-M356BXXU8DZE3/target.h` ou arquivo auxiliar foi
criado, pois isso exigiria fabricar valores. O próximo passo seguro é fornecer
os artefatos exatos listados em **Available artifacts**; somente então é
possível executar as verificações BTF/símbolos/disassembly/source e decidir se
a cadeia para no estágio inicial ou se há compatibilidade suficiente.