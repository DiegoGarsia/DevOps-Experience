# Virtual networks, bridges и VLAN

## Цель

Понять, как VM подключается к физической или виртуальной сети и где возникают ошибки VLAN, bridge, route и firewall.

## Prerequisites

Ethernet, ARP, IP, routing, namespaces и virtual machine model.

## In scope

Linux bridge работает как L2 switch: virtual NIC VM и physical uplink подключаются к bridge ports. VLAN trunk переносит несколько tagged VLAN, access port предоставляет одну untagged сеть. Ошибка tag на одном участке может оставить интерфейс up, но сделать соседей недостижимыми.

Типовой путь:

```text
VM vNIC → tap device → Linux bridge → physical NIC → switch
```

Guest обычно имеет собственный IP configuration, gateway и firewall. Host может применять filtering, NAT и forwarding отдельно от guest.

## Диагностика

```bash
bridge link
bridge vlan show
ip link
ip addr
ip route
tcpdump -eni <interface>
```

Сначала проверяйте link и MAC learning, затем VLAN tag, IP/prefix, route и firewall. Capture на tap, bridge и uplink помогает определить точку потери.

## Типичные ошибки

- смешать tagged и untagged configuration;
- забыть VLAN-aware bridge;
- настроить gateway в неправильной подсети;
- считать bridge router;
- фильтровать host traffic, не учитывая forwarding path.

## Практика

Создайте две VM в разных VLAN, настройте bridge и router, намеренно измените tag на одном участке и локализуйте отказ по packet capture.

## Следующие темы

`06-Backups-Restore-and-Recovery.md`, Proxmox networking и container networking.
