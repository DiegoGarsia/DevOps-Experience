# Network segmentation и security policies

## Цель

Ограничить lateral movement через network boundaries и проверяемые allowed flows.

## Prerequisites

Routing/firewall, CNI, NetworkPolicy, DNS, Services и threat modeling.

## In scope

Segmentation разделяет user, management, workload, database, storage и egress paths. Policy должна описывать source/destination, protocol/port, identity, direction, default behavior и exception owner.

## Практика

Разделите frontend/backend/database namespaces, примените default deny и разрешите только documented flows; проверьте egress и DNS.

## Следующие темы

`06-Supply-Chain-Signing-and-Image-Provenance.md`, vulnerability management и incident response.
