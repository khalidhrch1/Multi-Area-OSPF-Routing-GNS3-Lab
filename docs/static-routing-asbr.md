# Static Default Routing and OSPF ASBR — R5 ↔ R13 Extension

## Goal

R13 represents a **simulated ISP**, not a real Internet uplink. R5 is the OSPF edge router with a direct connection to R13. This experiment demonstrates the difference between *installing* a default route on one router and *advertising* it to an entire OSPF domain.

## Addressing

- R5 Gi2/0: `203.0.113.1/30`
- R13 Gi0/0: `203.0.113.2/30`
- R13 Loopback0: `198.51.100.1/24` (simulated external destination)

These TEST-NET addresses are reserved for documentation. They are not public Internet access.

## R5: install a static default route

```cisco
ip route 0.0.0.0 0.0.0.0 203.0.113.2
```

This modifies only R5's forwarding decision: unknown destinations are sent to R13. It does not inform R1–R12 automatically.

## R5: advertise the default through OSPF

```cisco
router ospf 1
 default-information originate
```

On Cisco IOS, the ordinary `default-information originate` requires a default route in R5's routing table. The `always` keyword removes this prerequisite; it is not recommended for this learning exercise because it can advertise an exit when R5 has no usable upstream route.

By originating an external default route, R5 becomes an **ASBR**. Other routers may learn `O*E2 0.0.0.0/0` through OSPF (metric/next hop vary). A Type 5 AS-external LSA is normally involved.

## R13: return paths

R13 must know where to send replies to internal sources. For example:

```cisco
ip route 10.0.0.0 255.0.0.0 203.0.113.1
ip route 1.1.1.1 255.255.255.255 203.0.113.1
```

The second example specifically supports testing ping with R1 Loopback0 as the source. Add other return routes if needed, or use NAT in a more realistic edge scenario. Setting opposing default routes on R5 and R13 can create a forwarding loop for unreachable destinations.

## Difference from `redistribute static subnets`

- `default-information originate` originates **only the default route** `0.0.0.0/0`.
- `redistribute static subnets` redistributes eligible static destinations, which is not the preferred method for simply telling all routers about an Internet exit.

## Verification

On R5:

```cisco
show ip route 0.0.0.0
show ip ospf database external 0.0.0.0
ping 203.0.113.2
ping 198.51.100.1
```

On R1:

```cisco
show ip route 0.0.0.0
ping 198.51.100.1
traceroute 198.51.100.1
```

Do not assume pings succeed unless both forward and return routes are present and the router interfaces are up/up.
