# Kubernetes Networking

## Путь внешнего запроса

```text
External Client
↓
Load Balancer / reverse proxy
↓
Ingress
↓
Ingress Controller
↓
Service
↓
EndpointSlice
↓
Pod IP
↓
container
```

Внутри cluster каждый Pod обычно получает уникальный IP и должен уметь обращаться к другому Pod без NAT между Pod networks. CNI создаёт interfaces/routes/правила доставки. Service даёт стабильный virtual IP и selector-based discovery; dataplane реализует forwarding через kube-proxy или другой механизм.

## Service types

- ClusterIP — внутренний virtual endpoint;
- NodePort — порт на nodes;
- LoadBalancer — внешний provider/integration;
- ExternalName — DNS alias без Pod endpoints.

Service не «ищет процесс»: controller строит EndpointSlice из matching labels и готовности Pod.

## DNS

CoreDNS получает Service/Pod records из API. `service.namespace.svc.cluster.local` позволяет name-based discovery. Pod DNS зависит от `/etc/resolv.conf`, DNS policy и доступности CoreDNS.

## NetworkPolicy

NetworkPolicy меняет allowed flows, если CNI её поддерживает. Политика действует по направлениям ingress/egress и selector; default deny требует явных разрешений DNS, application и dependencies.

## Диагностика

```bash
kubectl get svc,endpoints,endpointslices -n NS
kubectl describe svc SERVICE -n NS
kubectl get pod -o wide -n NS
kubectl exec -n NS POD -- getent hosts SERVICE
kubectl exec -n NS POD -- curl -sv http://SERVICE:PORT/health
kubectl get networkpolicy -A
kubectl logs -n kube-system deploy/coredns
```

## Дерево отказа

- Pod не видит Pod → Pod IP, CNI routes, NetworkPolicy, node path;
- Pod не видит Service → Service DNS, ClusterIP, selector, EndpointSlice;
- DNS не работает → `/etc/resolv.conf`, CoreDNS Pod/service, NetworkPolicy к DNS;
- Pod не выходит в Internet → egress policy, node route, NAT, firewall;
- внешний клиент не попадает → DNS, listener, LoadBalancer/Caddy, Ingress rule/TLS;
- Ingress получил 404 → Host/path rule или backend mapping;
- 502/503 → Service endpoints/readiness/targetPort/upstream.

## Вопросы

- **Почему Service может существовать без endpoints?** Selector не совпадает, Pods не Ready или EndpointSlice не создан.
- **CNI и kube-proxy?** CNI обеспечивает Pod network, kube-proxy/dataplane реализует Service forwarding; конкретная реализация может отличаться.
- **Почему `ping` не достаточно?** ICMP и TCP/HTTP policy могут различаться.

## Как объяснить за 30–60 секунд

«Я проверяю routing сверху вниз: внешний DNS и listener, Ingress rule, Service selector, EndpointSlice, readiness и Pod IP. Внутри cluster CNI даёт Pod connectivity, Service даёт стабильный virtual endpoint, CoreDNS — name discovery, NetworkPolicy — разрешённые flows».

[[01-Fundamentals/Networking/Layers-IP-TCP-UDP]], [[01-Fundamentals/Networking/DNS]], [[03-JuniorPlus/Kubernetes/Core-Architecture-and-Objects]], [[05-Production-Troubleshooting/Kubernetes]].

## Что делает CNI и dataplane

CNI plugin настраивает Pod interface, veth pair, IP allocation и маршруты. Service forwarding может выполнять kube-proxy через iptables/IPVS или другой dataplane. Эти компоненты не являются DNS и не являются application proxy.

## Service selector

```yaml
selector:
  app: backend
ports:
  - port: 80
    targetPort: 8080
```

`port` — порт Service, `targetPort` — порт Pod. Если application слушает только `127.0.0.1`, Service не сможет достучаться через Pod IP. Если `targetPort` ошибочен, EndpointSlice может быть правильным, но TCP connection будет refused.

## NetworkPolicy

Default deny ingress/egress должен сопровождаться explicit allow для DNS, frontend→backend, backend→database и monitoring. Политика действует по labels/namespaces и зависит от поддержки CNI. Наличие YAML не доказывает, что dataplane применил policy.

## Практическое задание

1. Проверь Pod-to-Pod по Pod IP и по Service name.
2. Сломай Service selector, `targetPort` и application bind address отдельно.
3. Введи default deny, затем разреши DNS и application flow.
4. Проверь egress в Internet и объясни роль NAT node.
5. Сымитируй DNS failure и сравни `getent`, `nslookup`, direct Service IP.
6. Для Ingress раздели 404, 503, 502 и TLS error.

## Что собирать

```bash
kubectl get pod -o wide
kubectl get svc,endpointslice -o yaml
kubectl exec POD -- cat /etc/resolv.conf
kubectl exec POD -- ip route
kubectl exec POD -- ss -ltnp
kubectl logs -n kube-system deploy/coredns
```

Каждая команда отвечает на отдельный вопрос: адрес, selected endpoints, DNS server, route, listener и resolver errors.
