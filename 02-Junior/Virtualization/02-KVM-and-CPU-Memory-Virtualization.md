# KVM и виртуализация CPU/памяти

## Цель

Понять, как KVM предоставляет guest доступ к CPU и memory и почему oversubscription влияет на latency.

## Prerequisites

Virtualization model, CPU scheduling, virtual memory и resource limits.

## In scope

KVM использует аппаратные extensions CPU, а QEMU обычно предоставляет VM device model. Guest kernel видит virtual CPUs и memory, но host scheduler распределяет реальные CPU между VM и другими задачами.

Memory virtualization включает shadow/EPT/NPT mappings, ballooning, page sharing и overcommit. Для DevOps важнее последствия: guest может видеть свободную память, когда host испытывает pressure, а latency зависит от steal time и memory reclaim.

vCPU count не должен автоматически равняться числу host CPUs. Большое количество vCPU может увеличить scheduling overhead и задержку, особенно при нескольких VM, NUMA и noisy neighbor.

## Диагностика

Сопоставляйте guest CPU usage с host CPU usage, steal time, ready/wait metrics hypervisor, memory balloon и swap. Проверяйте NUMA placement и CPU pinning только при доказанной потребности.

## Типичные ошибки

- выделять VM больше RAM, чем реально доступно;
- игнорировать steal time;
- использовать swap host как штатный memory tier для VM;
- считать CPU overcommit бесплатным;
- менять CPU topology без проверки guest OS.

## Практика

Запустите две VM с контролируемой нагрузкой, увеличьте vCPU одной и измерьте latency второй. Объясните результат через host scheduler и steal time.

## Следующие темы

`03-VM-Resources-and-Device-Model.md`, virtual disks и snapshots.
