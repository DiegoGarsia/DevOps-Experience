# NAT и conntrack

## Цель

Понять, как NAT изменяет адреса и порты, а conntrack связывает пакеты одного соединения в stateful firewall.

## Prerequisites

Routing, TCP/UDP, firewall и network namespaces.

## In scope

Source NAT изменяет source address/port при выходе из private network. Destination NAT изменяет destination и часто используется для port forwarding. После NAT таблица трансляции должна применяться согласованно к обратным пакетам.

Conntrack хранит состояние flow: tuple, protocol state, timeout и NAT mapping. Таблица имеет ограниченный размер. Её переполнение приводит к drop новых connections даже при низком CPU.

```text
private client: 10.0.0.10:50000
        ↓ SNAT
public endpoint: 198.51.100.5:41000
        ↓
server response
        ↓ reverse conntrack mapping
private client
```

NAT не является полноценной security policy: он меняет адреса, а разрешение трафика задаётся firewall. NAT также не исправляет неправильный routing внутри сети.

## Диагностика

```bash
conntrack -L
conntrack -S
ss -s
tcpdump -ni any 'host 10.0.0.10'
```

Сравнивайте пакет до и после точки NAT, смотрите counters и заполненность conntrack table. Учитывайте timeout разных протоколов и long-lived connections.

## Типичные ошибки

- использовать NAT вместо маршрутизации без необходимости;
- забывать hairpin NAT для доступа к собственному public address;
- считать conntrack бесконечным;
- менять только одну сторону port forwarding;
- увеличивать table size без оценки памяти и timeout.

## Практика

Настройте SNAT для private subnet и DNAT к test service, зафиксируйте оригинальный и преобразованный tuple, затем создайте много коротких connections и наблюдайте conntrack entries.

## Следующие темы

Networking diagnostics уровня JuniorPlus, containers networking и Kubernetes CNI.
