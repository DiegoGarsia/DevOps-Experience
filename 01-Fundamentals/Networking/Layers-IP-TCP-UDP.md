# Сети: Ethernet → IP → TCP/UDP

## Слои и инкапсуляция

```text
данные приложения
↓ сегмент TCP/UDP
↓ пакет IP
↓ кадр Ethernet
↓ физическая среда
```

Ethernet доставляет кадры внутри broadcast domain по MAC-адресам. ARP сопоставляет IPv4 с MAC. Для внешней подсети узел отправляет кадр на MAC gateway. IP маршрутизирует пакет, но не гарантирует доставку. TCP даёт упорядоченный надёжный byte stream, UDP — datagram без гарантии доставки.

## TCP под капотом

```text
SYN →
     ← SYN+ACK
ACK →
```

Состояния `SYN-SENT`, `SYN-RECV`, `ESTABLISHED`, `FIN-WAIT`, `TIME-WAIT` описывают состояние endpoint в ядре. Retransmission обрабатывает потери, receive window даёт flow control, congestion control ограничивает скорость отправки. `timeout` означает отсутствие успешного ответа, `refused` обычно означает RST/no listener, `reset` — принудительное закрытие peer или middlebox.

## Подсеть, gateway и NAT

Subnet mask разделяет network и host bits. Default gateway обрабатывает внешние назначения. NAT меняет address/port и хранит состояние в conntrack; NAT не заменяет routing или firewall.

## Команды

```bash
ip -br addr
ip route
ip route get 1.1.1.1
ip neigh
ss -s
ss -ltnp
nc -vz HOST PORT
ping -c 4 GATEWAY
traceroute HOST
mtr -rw -T -P 443 HOST
sudo tcpdump -ni any 'host IP and (tcp or icmp)'
```

`ip route get` показывает выбранный ядром путь. `ss` связывает endpoint, состояние и процесс. Packet capture отличает SYN без ответа, RST и завершённое рукопожатие.

## MTU и отказы

Несовпадение MTU может пропускать handshake, но ломать большие TLS/HTTP packets. Проверяй MTU интерфейса, VPN overhead, MSS/PMTUD и retransmissions. Asymmetric routing может отправить ответ через другой firewall path и сломать conntrack/reverse-path checks.

## Дерево диагностики

```text
имя
↓ DNS result
IP
↓ route/ARP
TCP connect
↓ listener/firewall/conntrack
TLS handshake
↓ certificate/SNI/time/CA
HTTP response
↓ proxy/upstream/application
```

## Вопросы

- **Почему ping не доказывает HTTP?** ICMP может быть разрешён, когда TCP/443 заблокирован.
- **Timeout и refused?** Timeout не получил ответа, refused получил явный reset.
- **Зачем TIME_WAIT?** Чтобы старые сегменты не повредили новое соединение и финальный ACK был обработан.

## Как объяснить за 30–60 секунд

«Я диагностирую сеть от интерфейса и маршрута к приложению. Ethernet/ARP доставляют локальный кадр, IP выбирает next hop, TCP создаёт stateful stream, а port/socket связывает поток с процессом. Timeout, refused, TLS error и HTTP 500 — разные наблюдения и требуют разных проверок».

[[01-Fundamentals/Networking/DNS]], [[01-Fundamentals/Networking/HTTP-TLS]], [[01-Fundamentals/Networking/Firewall-NAT-Proxy]].

## Пошаговый пример: открытие HTTPS

1. Приложение вызывает `getaddrinfo` для имени.
2. Resolver получает IP через DNS.
3. Kernel выбирает маршрут через routing table.
4. Для соседнего gateway выполняется ARP.
5. TCP отправляет SYN на destination port.
6. После SYN+ACK устанавливается connection state.
7. TLS использует SNI и проверяет certificate chain.
8. HTTP request передаётся в reverse proxy.
9. Proxy выбирает upstream и получает response.

На каждом шаге есть отдельный symptom. Это позволяет не лечить HTTP 500 через изменение ARP и не проверять сертификат при NXDOMAIN.

## TCP-параметры и симптомы

- retransmission увеличивает latency;
- receive window ограничивает поток при медленном receiver;
- congestion control снижает скорость после loss;
- `TIME_WAIT` нормален после активного закрытия, но чрезмерное количество может расходовать ephemeral ports;
- `SYN-RECV` показывает незавершённые handshakes и может указывать на loss или SYN flood;
- `ESTABLISHED` без движения может означать зависший application read.

```bash
ss -tan state time-wait | wc -l
ss -tan state syn-recv
ss -tin
sudo tcpdump -ni any 'tcp port 443'
```

`ss -tin` показывает TCP timers, retransmission и congestion information. Packet capture подтверждает факт на интерфейсе, но не объясняет application semantics без логов.

## Практическое задание

1. Запусти HTTP listener только на `127.0.0.1` и объясни, почему remote client его не видит.
2. Перенеси listener на `0.0.0.0`, но закрой port firewall; сравни timeout/reject.
3. Сломай route и отличи его от отказа listener.
4. Проверь MTU через `ping -M do` и сравни small/large packets.
5. Сними `tcpdump` для SYN, handshake, TLS и HTTP.

## Production implications

- один DNS ответ может попасть в разные cache в течение TTL;
- NAT усложняет трассировку source identity;
- stateful firewall может отбросить asymmetric return path;
- MTU-проблема часто проявляется только на больших payloads;
- load balancer нуждается в health check, connection draining и защите от retry storm.

## Дополнительные вопросы

- Почему TCP connection может быть установлен, но HTTP request зависает?
- Что изменяет NAT и где сохраняется состояние обратного потока?
- Чем L3 reachability отличается от L4 connectivity?
- Почему потеря ответа на промежуточном hop не всегда означает потерю до конечного host?
