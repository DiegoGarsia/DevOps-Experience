# Firewall, NAT и proxy

## Firewall

Firewall применяет policy к packets/flows: source, destination, protocol, port, interface и state. Stateful firewall хранит conntrack и разрешает established/related traffic. Порядок правил важен.

Без firewall каждый listening service доступен reachable clients. Но firewall не исправляет отсутствующий listener и не заменяет authentication.

```text
loopback/established → explicit allow → default deny
```

Перед remote change нужны console access, backup rules и rollback plan.

## NAT

SNAT/MASQUERADE меняет source при выходе, DNAT меняет destination для published service. Conntrack связывает обратный flow с исходным. NAT может скрывать topology, усложняя source identity и logging.

## Proxy и load balancing

Forward proxy действует от имени client, reverse proxy — от имени server. Load balancer выбирает upstream через round-robin, least connections, hash или weighted policy. Health check должен исключать unhealthy endpoint и не создавать thundering herd.

## Команды

```bash
sudo nft list ruleset
sudo iptables -L -n -v
sudo iptables -t nat -L -n -v
sudo conntrack -L
ss -ltnp
sudo tcpdump -ni any 'port 443'
curl -v --proxy http://PROXY:PORT https://HOST
```

Смотри counters, rule order, interface и state. `DROP` часто выглядит как timeout, `REJECT` — как immediate refusal.

## Отказы

- local curl работает, remote timeout → firewall/route/NAT;
- TCP установлен, затем reset → application/proxy/conntrack/MTU;
- upstream напрямую здоров, proxy отдаёт 502 → proxy route/Host/TLS/ACL;
- asymmetric path → stateful firewall drops return traffic;
- NAT скрывает client IP → forwarded headers должны быть доверенными только от известных proxy.

## Как объяснить за 30–60 секунд

«Firewall отвечает за policy, NAT — за address translation и state, proxy — за application mediation. Для timeout я проверяю route, ACL и conntrack, для refused — listener и reject, для 502 — proxy-to-upstream. Изменение policy начинаю с безопасного доступа и counters».

[[01-Fundamentals/Networking/Layers-IP-TCP-UDP]], [[01-Fundamentals/Networking/HTTP-TLS]], [[05-Production-Troubleshooting/Networking]].

## Пошаговая диагностика TCP timeout

1. Проверить локальный listener через `ss -ltnp`.
2. Проверить kernel route через `ip route get DESTINATION`.
3. Проверить ARP/neighbor для gateway.
4. С удалённого client выполнить `nc -vz`.
5. На server включить `tcpdump` и посмотреть, приходит ли SYN.
6. Проверить firewall counters и порядок rules.
7. Проверить NAT/conntrack на gateway.
8. Повторить тест после одной корректировки.

Интерпретация:

- SYN не приходит → upstream route/firewall/NAT;
- SYN приходит, ответа нет → local firewall или listener;
- SYN+ACK выходит, но ACK не возвращается → обратный путь/conntrack/asymmetric routing;
- TCP установлен, HTTP не отвечает → proxy/application/timeout.

## Пример stateful policy

```text
allow loopback
allow established,related
allow SSH only from administration network
allow HTTP/HTTPS to reverse proxy
allow required DNS/monitoring
log and deny remaining inbound
```

Сначала добавляй allow для текущего administrative session и established traffic, затем меняй default policy. Для удалённой системы готовь console rollback.

## Практическое задание

1. Подними listener на loopback и на all interfaces, сравни доступность.
2. Создай `DROP` и `REJECT`, сравни результаты client.
3. Опубликуй internal service через DNAT и объясни обратный SNAT.
4. Проверь conntrack для established flow.
5. Настрой reverse proxy с двумя upstream, отключи один и наблюдай health behavior.

## Production implications

- firewall rule без owner, scope и expiration превращается в неуправляемый risk;
- NAT затрудняет forensic source identity;
- forwarded headers должны приниматься только от trusted proxy;
- connection tracking table может исчерпаться при burst;
- default deny без observability создаёт silent outage.

## Дополнительные вопросы

- Почему `DROP` и `REJECT` по-разному влияют на client latency?
- Как asymmetric routing ломает stateful firewall?
- Чем forward proxy отличается от reverse proxy по identity и trust boundary?
- Почему health check может быть green, когда user request получает 500?
