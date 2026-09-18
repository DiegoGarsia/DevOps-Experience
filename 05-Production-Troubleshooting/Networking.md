# Networking troubleshooting guide

## Дерево

```text
DNS
↓
IP/route/ARP
↓
TCP/UDP/port/socket
↓
firewall/NAT/conntrack
↓
TLS/SNI/CA/time
↓
HTTP/proxy/upstream
↓
application
```

## DNS

`resolvectl status`, `dig`, `dig @SERVER`, TTL и authority. Если имя не разрешается, не переходи к HTTP. Сравни client и probe source: у них могут быть разные resolver и routing domains.

## TCP

`ip route get`, `ip neigh`, `nc -vz`, `ss -ltnp`, `tcpdump`. SYN без ответа — path/firewall/listener, RST — explicit refusal/peer reset, established с retransmissions — MTU, loss, congestion или application read.

## TLS/HTTP

`openssl s_client -servername`, `curl -vk`, `curl -w`. Проверяй SAN, chain, trust store, clock, SNI и конкретный компонент termination. 502/504 ищи между proxy и upstream, 500 — в application/dependency.

## Root cause/prevention

Запиши scope (один client или все), source IP и timestamp. Добавь synthetic probe из реального network location, certificate expiry alert, firewall rule review и tests для DNS/route/TLS.

[[05-Production-Troubleshooting/Universal-Method]], [[01-Fundamentals/Networking/Layers-IP-TCP-UDP]], [[01-Fundamentals/Networking/HTTP-TLS]].

## Матрица интерпретации

| Наблюдение | Следующая гипотеза |
|---|---|
| DNS NXDOMAIN | zone/record/name, не TCP |
| DNS работает, TCP timeout | route/firewall/conntrack/listener |
| TCP refused | listener или explicit reject |
| TCP установлен, x509 | SAN/CA/SNI/time/trust store |
| TLS установлен, 502 | proxy-to-upstream |
| 503 без endpoints | readiness/Service selector |
| 500 | application/dependency |

## Сценарий: с ноутбука работает, с Blackbox нет

Сравни source network, resolver, route, proxy environment, CA bundle, SNI и timeout. Blackbox может находиться внутри cluster и видеть другой DNS/egress. Проверка с другого source не доказывает доступность service для monitor.

## Prevention

Synthetic checks должны измерять DNS, TCP, TLS и HTTP отдельно; certificate expiry и DNS latency должны иметь alerts; firewall changes должны иметь owner, source/destination, expiration и rollback.
