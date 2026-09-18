# TCP, UDP, ports и sockets

## Цель

Понять, как IP-пакет связывается с процессом и почему «порт открыт» и «приложение работает» — не одно и то же.

## Prerequisites

IP, routing и базовая инкапсуляция.

## In scope

IP доставляет пакет host-у, а transport protocol доставляет данные процессу через port. Socket описывает endpoint и параметры соединения. Для TCP 4-tuple обычно включает source IP/port и destination IP/port; это позволяет одному серверному порту обслуживать много клиентов.

TCP — connection-oriented byte stream с sequence numbers, acknowledgements, retransmission, flow control и congestion control. UDP — datagram protocol без встроенной гарантии доставки, порядка или уникальности; эти свойства реализует приложение, если они нужны.

Порт `0–65535` — логический номер, а не физический разъём. Listening socket принимает новые соединения, established socket обслуживает конкретный поток. Один процесс может слушать IPv4 и IPv6 отдельно или через dual-stack, что зависит от настроек.

```text
Application
  ↓ socket API
TCP/UDP port
  ↓
IP address
  ↓
Network interface
```

## Диагностика

```bash
ss -ltnp
ss -tanp
ss -lunp
lsof -nP -iTCP:8080
```

Проверяйте listening address: `127.0.0.1:8080` доступен только локально, `0.0.0.0:8080` — на всех IPv4-интерфейсах при разрешённом firewall.

## Типичные ошибки

- проверять только наличие процесса;
- путать UDP listener с TCP listener;
- считать `connection refused` сетевым timeout;
- публиковать management port на всех интерфейсах;
- забывать про IPv6 listener.

## Практика

Запустите TCP и UDP сервисы на разных портах, проверьте их через `ss`, измените bind address и зафиксируйте разницу между refused, timeout и успешным соединением.

## Следующие темы

`06-TCP-Lifecycle-Retransmissions-and-Timeouts.md` и `12-HTTP-Connection-Lifecycle-and-Timeouts.md`.
