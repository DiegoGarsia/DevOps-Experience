# Firewall и packet filtering

## Цель

Понять, как firewall принимает решение по пакету и как безопасно диагностировать блокировку без временного отключения всей защиты.

## Prerequisites

Ethernet, IP, routing, TCP/UDP и sockets.

## In scope

Firewall сопоставляет пакет с правилами по интерфейсу, адресу, порту, протоколу и connection state. Правила имеют порядок, policy по умолчанию и действие accept/drop/reject/log. Stateful firewall использует conntrack и может разрешить обратный трафик для уже установленного соединения.

Уровни фильтрации могут находиться на host, router, load balancer, security group или network policy. Ошибка на любом уровне выглядит как timeout или refusal, поэтому нужно знать путь пакета и точку применения правила.

Безопасная политика:

1. разрешить необходимый management access;
2. разрешить только нужные service ports;
3. ограничить source networks;
4. добавить логирование отказов с rate limit;
5. проверить обратный трафик;
6. иметь rollback или out-of-band access.

## Диагностика

Сначала проверьте listener и route, затем counters правила и packet capture с обеих сторон. Отличайте `DROP` без ответа от `REJECT` с немедленным сообщением.

## Типичные ошибки

- удаление всех правил вместо точечного изменения;
- разрешение `0.0.0.0/0` для SSH;
- отсутствие stateful reverse rule;
- логирование каждого пакета без ограничения;
- проверка firewall внутри host, когда блокирует внешний ACL.

## Практика

Создайте default-deny правила для тестового сервера, разрешите SSH только с jump host и HTTP от клиента, затем соберите counters и packet capture для разрешённого и заблокированного запроса.

## Следующие темы

`18-NAT-and-Conntrack.md`, Junior firewall operations и network troubleshooting.
