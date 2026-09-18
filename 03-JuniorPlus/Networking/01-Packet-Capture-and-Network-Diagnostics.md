# Packet capture и network diagnostics

## Цель

Собирать минимально необходимое evidence и читать packet flow по слоям.

## Prerequisites

Ethernet, IP, routing, TCP, DNS, firewall и namespaces.

## In scope

Capture отвечает, был ли пакет отправлен, получен, изменён, потерян или отвергнут. Фильтруйте по host/port/protocol/time и сопоставляйте с socket state и application logs. Capture на разных interfaces помогает найти boundary.

## Практика

Снимите DNS, TCP handshake, TLS и HTTP flow, затем создайте timeout, RST и MTU failure и сопоставьте различия.

## Следующие темы

`02-Advanced-Routing-VLANs-and-Policy-Routing.md`, DNS и HTTP diagnostics.
