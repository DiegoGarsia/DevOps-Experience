# Capacity planning и resource bottlenecks

## Цель

Находить ограничивающий ресурс и планировать headroom, peak, growth и recovery capacity.

## Prerequisites

Metrics, requests/limits, database connections, storage/network performance и SLO.

## In scope

Capacity model учитывает throughput, concurrency, latency, resource saturation, redundancy и failure reserve. Bottleneck может перемещаться: увеличение replicas переносит ограничение в database connections, storage или network.

## Практика

Постройте baseline/load model для web+DB, увеличьте traffic, найдите first bottleneck и рассчитайте capacity с отказом одного node.

## Следующие темы

`07-Resilience-and-Chaos-Testing-Concepts.md`, StrongMiddle architecture.
