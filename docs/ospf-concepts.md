# OSPF Fundamentals

## What is OSPF?

OSPFv2 (RFC 2328) is a link-state Interior Gateway Protocol (IGP). Routers exchange LSAs, maintain a Link-State Database (LSDB) for each attached area, run the Dijkstra SPF algorithm, and install selected routes in the IPv4 routing table.

## Useful identifiers

- **Router ID**: unique 32-bit ID such as `2.2.2.2`; it does not have to be a reachable interface IP, although a loopback is a common choice.
- **Process ID**: local Cisco IOS configuration identifier (`router ospf 1`); neighboring routers do not need identical process IDs.
- **Area ID**: OSPF area identifier. Area `0` is the backbone.
- **Cost**: additive OSPF path metric. Interface cost is normally based on reference bandwidth unless manually configured.

## Neighbors and states

Typical neighbor state progression: `Down → Init → 2-Way → ExStart → Exchange → Loading → Full`.

On broadcast Ethernet links OSPF elects a DR and BDR. An adjacency can be `FULL/DR` or `FULL/BDR`; two DROTHER routers can remain 2-Way with one another. On point-to-point virtual links, there is no DR/BDR election, so the display can show `FULL/-`.

## Router types

- **Internal router**: all attached OSPF interfaces are in one area.
- **Backbone router**: has an interface attached to Area 0.
- **ABR**: connects non-backbone area(s) to the backbone; may have a logical Area 0 connection through a virtual link.
- **ASBR**: introduces external routing information (such as an external default route) into OSPF.

## Common IPv4 route codes on Cisco IOS

| Code | Meaning |
|---|---|
| `C` | Connected route |
| `S` | Static route |
| `O` | OSPF intra-area |
| `O IA` | OSPF inter-area |
| `O E1` | OSPF external Type 1; combines external and internal costs |
| `O E2` | OSPF external Type 2; external metric generally dominates, internal cost breaks ties |

## Common LSAs in normal OSPF areas

| Type | Name | Originated by |
|---|---|---|
| 1 | Router LSA | Every router in an area |
| 2 | Network LSA | DR on a broadcast/NBMA transit segment |
| 3 | Summary LSA | ABR to announce destinations across areas |
| 4 | ASBR Summary LSA | ABR to advertise reachability to an ASBR where needed |
| 5 | AS External LSA | ASBR to advertise external destinations, including a default route |

Type 7 LSAs exist in NSSAs, which are not part of the current design.

## Lab commands

```cisco
show ip ospf neighbor
show ip ospf database
show ip route ospf
show ip ospf border-routers
show ip ospf interface brief
```
