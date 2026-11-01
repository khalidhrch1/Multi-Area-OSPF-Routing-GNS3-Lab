# OSPF Virtual Links in This Topology

## Why they are necessary

In conventional multi-area OSPF, all non-backbone areas connect logically to Area 0 and Area 0 is contiguous. The GNS3 design has two problems:

1. **Area 7** reaches Area 0 only through **Area 5**.
2. The **central** and **right** portions of Area 0 are separated by **Area 6**.

A virtual link establishes a logical point-to-point Area 0 adjacency **across an existing non-backbone transit area**. It does not create a physical connection, change interface subnets, or change the transit area's Area ID.

## Left virtual link: R2 ↔ R3 (through Area 5)

R2 already participates in Areas 7 and 5. R3 participates in Areas 5 and 0. The virtual link allows R2 to attach logically to Area 0 through R3.

R2:

```cisco
router ospf 1
 area 5 virtual-link 3.3.3.3
```

R3:

```cisco
router ospf 1
 area 5 virtual-link 2.2.2.2
```

The last parameter is the **remote OSPF Router ID**, not an interface address.

## Right virtual link: R8 ↔ R11 (through Area 6)

R8 and R11 both attach to Area 6 and to opposite halves of Area 0. The expected virtual configuration:

R8:

```cisco
router ospf 1
 area 6 virtual-link 11.11.11.11
```

R11:

```cisco
router ospf 1
 area 6 virtual-link 8.8.8.8
```

R9 does **not** need the `area ... virtual-link` command. It acts as an ordinary transit router in Area 6. R8 ↔ R10 is another valid pair if desired; do not configure both pairs without a purposeful redundancy test.

## Prerequisites

- Both endpoints participate in a common transit area and can reach one another within that area.
- The transit area is **not** a stub area or NSSA.
- The remote Router IDs are correct, the OSPF area configuration matches, and the transit area is healthy.
- The backbone must be connected through at least one virtual link endpoint.

## Verification

```cisco
show ip ospf virtual-links
show ip ospf neighbor
show ip ospf border-routers
show ip route ospf
```

A working virtual adjacency should be **FULL**. A router can list the same neighbor Router ID twice: once for the physical adjacency in transit Area 5, and once for the virtual Area 0 adjacency (`OSPF_VL0`). The two adjacencies can share the same physical next hop.

## Lab result actually observed

R2 showed:

```text
3.3.3.3   0   FULL/-     10.0.30.2   OSPF_VL0
3.3.3.3   1   FULL/DR    10.0.30.2   GigabitEthernet0/0
1.1.1.1   1   FULL/BDR   10.0.40.1   GigabitEthernet1/0
```

This verifies the R2–R3 virtual neighbor was FULL when the command was captured. No equivalent console proof for R8–R11 is included yet.

## Diagnostic idea

Compare the network before and after enabling one virtual link. Record `show ip ospf database` and `show ip route ospf` on R1 or R12 to understand which inter-area routes are available. A successful ping alone does not prove backbone design correctness.

## Reference

[RFC 2328, Section 15 — Virtual Links](https://www.rfc-editor.org/rfc/rfc2328#section-15)
