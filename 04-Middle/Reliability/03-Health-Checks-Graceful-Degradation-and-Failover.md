# Health checks, graceful degradation и failover

## Цель

Проектировать отказ так, чтобы сервис сохранял критическую функцию и не создавал cascade failure.

## Prerequisites

Load balancing, probes, retries/timeouts, replication и SLO.

## In scope

Health signal должен соответствовать decision: route traffic, restart process или trigger failover. Graceful degradation отключает non-critical feature, использует cache/default или уменьшает fidelity. Failover требует fencing, data safety, client reconnection и verification.

## Практика

Смоделируйте отказ dependency, slow backend и primary database, задайте degraded response и измерьте user impact.

## Следующие темы

`04-RPO-RTO-and-Recovery-Strategies.md`, circuit breakers и DR.
