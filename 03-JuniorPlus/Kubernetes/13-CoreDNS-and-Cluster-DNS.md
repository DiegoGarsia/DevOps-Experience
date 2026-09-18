# CoreDNS и cluster DNS

## Цель

Понять, как Pod разрешает Service name и где искать проблему между application, CoreDNS, upstream и network policy.

## Prerequisites

DNS resolution path, Services, Pods, networking и ConfigMaps.

## In scope

CoreDNS работает как cluster DNS service и отвечает за имена Service/Pod, forwarding внешних имён и plugin chain. Pod получает `resolv.conf` с cluster nameserver, search domains и options.

Service DNS имя зависит от namespace и формы записи. Короткое имя ищется через search path, полное имя содержит service и cluster domain. Внешний DNS запрос может идти через CoreDNS forwarding configuration.

## Диагностика

Проверяйте DNS Pod, Service `kube-dns`, CoreDNS Pods/logs, ConfigMap, EndpointSlice, network policy и upstream resolver. Отличайте `NXDOMAIN`, `SERVFAIL`, timeout и неправильный адрес.

## Типичные ошибки

- тестировать DNS с host вместо Pod;
- считать CoreDNS причиной при отсутствии Service endpoints;
- слишком длинный search path;
- менять CoreDNS ConfigMap без config validation;
- забывать UDP/TCP DNS traffic в policy.

## Практика

Проверьте resolution Service и внешнего имени из Pod, сломайте CoreDNS upstream и NetworkPolicy по отдельности, восстановите цепочку.

## Следующие темы

`14-ConfigMaps-and-Application-Configuration.md`, Services и CNI.
