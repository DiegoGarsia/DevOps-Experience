# Эксплуатация host firewall и NAT

## Цель

Безопасно менять правила доступа и диагностировать SNAT/DNAT на Linux host, не теряя management access.

## Prerequisites

Routing, firewall, conntrack, services/listeners и namespaces.

## In scope

Host firewall фильтрует packet path по interface, address, protocol, port и connection state. Сначала определите policy: какие входящие, исходящие и forwarded flows разрешены. Затем добавляйте узкие правила с counters и логированием отказов.

NAT меняет source или destination address/port, а conntrack поддерживает обратное преобразование. Port forwarding требует правил DNAT, filter policy и обратного маршрута. Hairpin traffic может потребовать отдельной обработки.

Безопасная последовательность изменения:

1. открыть резервную SSH-сессию;
2. проверить текущие rules/counters;
3. добавить временное узкое правило;
4. проверить доступ и обратный трафик;
5. сохранить конфигурацию с rollback;
6. удалить временное правило.

## Диагностика

Сопоставляйте `ss`, `ip route`, counters firewall, conntrack entries и `tcpdump`. `timeout` означает, что пакет мог быть отброшен или потерян; `connection refused` обычно означает достижение host без listener.

## Типичные ошибки

- менять удалённый firewall без console/out-of-band доступа;
- разрешать SSH из всего интернета;
- включать DNAT без filter rule;
- забывать forwarding и reverse route;
- считать NAT security control.

## Практика

Создайте default-deny policy, разрешите SSH с jump host и DNAT на test HTTP service, проверьте counters и выполните полный rollback.

## Следующие темы

Virtualization networking, container networking и Kubernetes CNI.
