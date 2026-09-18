# TCP lifecycle, retransmissions и timeouts

## Цель

Понять жизненный цикл TCP-соединения и отличать отказ установления, задержку подтверждения, retransmission и timeout приложения.

## Prerequisites

TCP, ports, sockets и routing.

## In scope

TCP устанавливает состояние через three-way handshake: SYN, SYN-ACK, ACK. После этого стороны обмениваются sequence numbers и acknowledgements. FIN завершает поток штатно, RST немедленно сбрасывает соединение.

Состояния вроде `LISTEN`, `SYN-SENT`, `SYN-RECV`, `ESTABLISHED`, `FIN-WAIT`, `CLOSE-WAIT` и `TIME-WAIT` описывают разные участки lifecycle. `CLOSE-WAIT` часто указывает, что удалённая сторона закрылась, а приложение ещё не закрыло свой descriptor. `TIME-WAIT` защищает от старых сегментов и нужен для корректного закрытия.

Retransmission означает отсутствие ожидаемого подтверждения в заданный период. Причиной могут быть потеря, перегрузка, firewall, неправильный MTU или перегруженный endpoint. Timeout — политика ожидания, а не обязательно доказательство конкретной причины.

## Диагностика

```bash
ss -tan state time-wait
ss -tan state close-wait
tcpdump -ni any 'tcp port 443'
nstat -az | grep -i retrans
```

Сопоставляйте packet capture, TCP states, application logs и latency. Один `SYN` без ответа отличается от `RST` и от установленного соединения, которое зависло во время передачи.

## Типичные ошибки

- увеличивать timeout, не найдя потерю или блокировку;
- считать `TIME-WAIT` утечкой;
- путать `CLOSE-WAIT` с удалённым закрытием всего соединения;
- проверять только client-side capture;
- не учитывать retry multiplication на верхнем уровне.

## Практика

Наблюдайте успешный handshake, отказ при закрытом порте и timeout при фильтрации пакетов. Для каждого сценария составьте таблицу состояний и фактов.

## Следующие темы

`07-MTU-Fragmentation-and-Path-MTU.md`, DNS и HTTP connection lifecycle.
