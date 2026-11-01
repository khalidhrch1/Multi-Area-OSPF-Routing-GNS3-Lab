# Verification Notes

The commands and example outcomes below are intentionally separated into **observed evidence** and **planned checks**.

## Observed in this lab conversation

1. R1 (`Gi0/0`) and R6 (`Gi0/0`) were configured as `10.0.10.1/30` and `10.0.10.2/30`; initial ping failure was resolved according to the lab operator.
2. R1 OSPF routing table showed intra-area routes to `2.2.2.2/32` and `6.6.6.6/32` before establishing inter-area backbone reachability.
3. R2's `show ip ospf neighbor` showed a FULL R2–R3 **virtual** adjacency on `OSPF_VL0`, FULL R2–R3 physical adjacency in Area 5, and FULL R2–R1 physical adjacency in Area 7. Exact output saved in `observed-R2-neighbors.txt`.
4. R2 `show ip ospf border-routers` included R3 (`3.3.3.3`) in Area 0 and Area 5, and R8 (`8.8.8.8`) in Area 0, demonstrating the route calculations at that point.

## Still to verify before marking the lab complete

- [ ] Physical OSPF neighbor tables from all routers R1–R12
- [ ] R8–R11 virtual adjacency in FULL state
- [ ] `O IA` routes and successful Loopback-to-Loopback pings across Areas 7, 5, 0 and 6
- [ ] R5–R13 static default route and return-path tests
- [ ] R1 learns an `O*E2` default after R5's `default-information originate`
- [ ] GNS3 project export can be imported with legally obtained IOS images

## Suggested collection commands

```cisco
show ip interface brief
show ip ospf neighbor
show ip ospf virtual-links
show ip ospf border-routers
show ip ospf database
show ip route ospf
show ip route 0.0.0.0
```

Save actual router outputs as sanitized text files in this directory. Avoid credentials, secrets, vendor binary images or unrelated personal files.
