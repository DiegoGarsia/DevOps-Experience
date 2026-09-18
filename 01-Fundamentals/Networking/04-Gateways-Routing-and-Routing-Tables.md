# Gateway, routing и routing table

## Цель

Понимать, как host выбирает next hop для IP-пакета и где искать причину недостижимости сети.

## Prerequisites

IP-адресация, subnetting, CIDR и ARP.

## In scope

Routing table содержит destination prefix, next hop, device, metric и source policy. Сначала выбирается самый длинный подходящий prefix, затем учитываются metric и дополнительные правила. Default route `0.0.0.0/0` используется, если более специфичного маршрута нет.

Gateway — это next hop, через который пакет покидает локальную подсеть. Он не обязан быть конечным сервером и не изменяет destination IP при обычной маршрутизации. На следующем узле решение принимается заново.

```text
Приложение
  ↓ destination IP
Routing table
  ↓ next hop + interface
ARP/neighbor resolution
  ↓
Ethernet frame → gateway → следующий router
```

Статический маршрут прост и предсказуем, но требует ручного обслуживания. Dynamic routing protocols решают задачу обмена маршрутами, однако добавляют convergence time, policy и failure modes.

## Диагностика

```bash
ip route
ip route get 203.0.113.10
ip rule
ip route show table all
traceroute -n 203.0.113.10
```

`traceroute` показывает не полную истину: firewall может фильтровать TTL-expired responses, а разные потоки могут идти разными маршрутами. Сначала проверяйте локальную таблицу и source address.

## Типичные ошибки

- добавить default route вместо корректного специфичного маршрута;
- забыть обратный маршрут;
- проверять только путь туда;
- игнорировать policy routing и несколько интерфейсов;
- менять routing table, не имея резервного доступа.

## Практика

Создайте три узла и две подсети, настройте forwarding на router, добавьте статические маршруты и проверьте путь туда и обратно после удаления одного маршрута.

## Следующие темы

`05-TCP-UDP-Ports-and-Sockets.md`, затем DNS и HTTP.
