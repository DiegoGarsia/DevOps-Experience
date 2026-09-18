# Ethernet, MAC и ARP

## Цель

Понять, как узлы находят друг друга в локальном сегменте и почему IP-адрес сам по себе не используется для доставки Ethernet-кадра.

## Prerequisites

Сетевые модели и инкапсуляция.

## In scope

Ethernet передаёт frames внутри broadcast domain. MAC-адрес идентифицирует сетевой интерфейс на link layer; коммутатор обучается соответствию MAC и порта по исходным кадрам.

Чтобы отправить IP-пакет соседу, host должен знать MAC назначения. Для IPv4 это делает ARP: узел рассылает broadcast request «кто владеет IP», а владелец отвечает своим MAC. Результат временно хранится в ARP cache.

Для удалённой сети host ищет MAC не конечного сервера, а next-hop gateway. Поэтому ARP может работать, даже если конечный IP находится в другой подсети.

```text
IP destination
  ↓ routing decision
next hop IP
  ↓ ARP
next hop MAC
  ↓
Ethernet frame
```

VLAN разделяет broadcast domains на уровне коммутатора. Ошибки native/tagged VLAN могут выглядеть как отсутствие IP connectivity, хотя интерфейс физически поднят.

## Диагностика

```bash
ip link
ip neigh
arping -I eth0 192.0.2.1
tcpdump -ni eth0 arp
```

Проверяйте состояние интерфейса, наличие соседской записи, дублирование IP и то, проходит ли ARP request/response.

## Типичные ошибки

- искать MAC удалённого сервера вместо gateway;
- считать `REACHABLE` постоянным состоянием ARP;
- путать L2 broadcast domain с IP subnet;
- игнорировать VLAN mismatch;
- использовать ARP как security boundary.

## Практика

Создайте два узла в одном сегменте, очистите neighbour cache, зафиксируйте ARP exchange через `tcpdump`, затем поместите узлы в разные VLAN и сравните поведение.

## Следующие темы

`03-IP-Addressing-Subnetting-and-CIDR.md` и `04-Gateways-Routing-and-Routing-Tables.md`.
