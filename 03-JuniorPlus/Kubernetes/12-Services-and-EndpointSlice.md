# Services и EndpointSlice

## Цель

Понять, как стабильное имя и virtual IP Service направляют трафик к динамическим Pod endpoints.

## Prerequisites

Pods, labels/selectors, TCP/IP, kube-proxy concept и controllers.

## In scope

Service — stable abstraction над изменяемыми Pod IP. Selector controller находит matching Pods и публикует EndpointSlices. kube-proxy или dataplane реализует forwarding к endpoints.

Типы ClusterIP, NodePort и LoadBalancer отличаются способом публикации. `targetPort` относится к Pod, `port` — к Service, а `nodePort` — к node exposure. Пустой EndpointSlice может быть следствием selector mismatch, readiness или отсутствия matching Pods.

## Диагностика

Проверяйте Service selector, endpoints, Pod labels/readiness, targetPort, DNS name, routing и dataplane rules. Разделяйте `no endpoints`, `connection refused`, timeout и application error.

## Типичные ошибки

- selector не совпадает с labels;
- перепутаны `port` и `targetPort`;
- Service направляет к not-ready Pod;
- использовать Pod IP напрямую;
- считать LoadBalancer доступным без external controller.

## Практика

Создайте Deployment и ClusterIP Service, сломайте selector и targetPort по отдельности, найдите расхождение через Service и EndpointSlice.

## Следующие темы

`13-CoreDNS-and-Cluster-DNS.md`, Ingress и network troubleshooting.
