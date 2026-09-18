# Docker bridge networking, ports и DNS

## Цель

Понять путь трафика между контейнерами и host и диагностировать разницу между container port и published host port.

## Prerequisites

Container network namespace, bridge, IP/routing, NAT и DNS.

## In scope

User-defined bridge network предоставляет контейнерам virtual Ethernet, IP и встроенное service discovery по имени. Default bridge имеет более ограниченное поведение и хуже подходит для явной application topology.

Port publishing создаёт forwarding host port → container address/port. Это не меняет port, который слушает процесс внутри. Приложение должно bind к подходящему interface, а host firewall должен разрешить нужный путь.

Container DNS обычно резолвит имя только внутри user-defined network. Изменение container IP не должно использоваться как configuration contract.

## Диагностика

Проверяйте `docker network inspect`, network namespace, listener внутри контейнера, published mapping, route, DNS и firewall. Тестируйте из того же network context, где находится клиент.

## Типичные ошибки

- обращаться к `localhost` вместо имени другого контейнера;
- использовать container IP в конфигурации;
- слушать только `127.0.0.1` внутри контейнера;
- публиковать database port на весь host;
- путать DNS failure и connection refused.

## Практика

Создайте frontend/backend/database сети, ограничьте публикацию только frontend, сломайте DNS alias и published port, затем восстановите connectivity.

## Следующие темы

`08-Compose-Multi-Container-Applications.md`, registries и Kubernetes Services.
