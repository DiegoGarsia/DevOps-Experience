# Advanced routing, VLAN и policy routing

## Цель

Диагностировать multi-interface host, VLAN и выбор route по source/policy.

## Prerequisites

Subnetting, gateways, routing tables, Ethernet/VLAN и firewall.

## In scope

Policy routing использует rules и отдельные tables; source address может определить другой next hop. VLAN разделяет L2 domains, но требует согласованности tags на всём пути. Asymmetric routing может ломать stateful firewall.

## Практика

Настройте две подсети и два uplink с policy rules, создайте asymmetric path и локализуйте отказ через `ip route get` и capture.

## Следующие темы

`03-DNS-Troubleshooting-and-Split-Horizon.md`, packet capture и Kubernetes networking.
