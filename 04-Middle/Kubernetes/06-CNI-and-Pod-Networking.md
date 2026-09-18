# CNI и Pod networking

## Цель

Понять, как CNI создаёт Pod network namespace, IP, route и connectivity между nodes.

## Prerequisites

Linux namespaces, veth/bridge, routing, NAT, Services и CRI.

## In scope

CNI plugin вызывается runtime при создании/удалении Pod sandbox. Он создаёт interface, назначает IP, настраивает routes, overlay/underlay, policy и иногда encryption. Pod-to-Pod traffic может пересекать node и encapsulation boundary.

Service traffic реализуется отдельно dataplane kube-proxy/eBPF/load balancer. Поэтому Pod IP connectivity, Service VIP и Ingress — разные проверки.

## Диагностика

Смотрите Pod IP, node route, veth, CNI logs, node interface, MTU, overlay peer и NetworkPolicy. Capture внутри Pod, на node и между nodes локализует boundary.

## Типичные ошибки

- MTU overlay меньше host;
- CNI IPAM исчерпан;
- node имеет неправильный route;
- Service работает, но Pod-to-Pod нет;
- CNI upgrade без compatibility plan.

## Практика

Проверьте Pod communication на одном и разных nodes, затем измените MTU или CNI route в lab и восстановите connectivity.

## Следующие темы

`07-NetworkPolicy-and-Cluster-Segmentation.md`, Ingress и cluster troubleshooting.
