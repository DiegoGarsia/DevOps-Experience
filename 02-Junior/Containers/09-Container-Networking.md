# Сетевое взаимодействие контейнеров

## Цель

Понять network namespace, virtual Ethernet pair, bridge, NAT и port publishing в container runtime.

## Prerequisites

Ethernet, IP, routing, NAT/conntrack, namespaces и sockets.

## In scope

Контейнер получает network namespace с интерфейсом и routing table. veth pair соединяет его с host-side bridge. Runtime может выдавать address через bridge network, выполнять NAT и публиковать port host → container.

```text
container eth0
    ↓ veth
host bridge
    ↓ NAT/route
host NIC → external network
```

Container DNS обычно задаётся runtime и может отличаться от `/etc/resolv.conf` host. Published port не означает, что приложение слушает правильный address внутри: service должен слушать `0.0.0.0` или соответствующий interface.

## Диагностика

```bash
ip netns list
ip link
ip addr
ip route
ss -ltnp
tcpdump -ni any port 8080
```

Выполняйте проверку из правильного namespace: host connectivity не доказывает container connectivity.

## Типичные ошибки

- приложение слушает только loopback внутри контейнера;
- опубликован неверный host port;
- DNS работает на host, но не в container;
- firewall фильтрует forwarding/NAT;
- отсутствие обратного маршрута.

## Практика

Создайте два контейнера в одной bridge network, проверьте service discovery по имени, опубликуйте port наружу, затем сломайте DNS и NAT по отдельности.

## Следующие темы

`10-Container-Runtime-Troubleshooting.md`, Docker bridge networking и Kubernetes CNI.
