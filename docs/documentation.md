# Technical Documentation — Multi-Layer Switching Lab

## 1. Purpose

This document describes the architecture, addressing plan, device roles, and verification procedures for the Multi-Layer Switching lab implemented in Cisco Packet Tracer.

The objective is to demonstrate how a multilayer switch can perform inter-VLAN routing while a Layer 2 switch provides access connectivity for end devices.

## 2. Network Architecture

The lab contains three main network devices:

| Device | Role |
|---|---|
| SW1 | Layer 2 access switch for IT and SALES workstations |
| MLS | Multilayer switch providing VLAN interfaces and inter-VLAN routing |
| R1 | Edge router connecting the internal network to a simulated external network |

The end devices are distributed across three VLANs: IT, SERVERS, and SALES.

Refer to [`topology.png`](topology.png) for the physical and logical topology.

## 3. VLAN Design

| VLAN ID | Name | Network | SVI / Default Gateway |
|---:|---|---|---|
| 10 | IT | `10.0.0.0/26` | `10.0.0.62` |
| 20 | SERVERS | `10.0.0.64/26` | `10.0.0.126` |
| 30 | SALES | `10.0.0.128/26` | `10.0.0.190` |

The `/26` prefix provides 64 addresses per subnet, including the network and broadcast addresses.

### Host addressing

| Device | VLAN | IP Address |
|---|---:|---|
| PC1 | 10 | `10.0.0.1` |
| PC2 | 10 | `10.0.0.2` |
| PC6 | 10 | `10.0.0.3` |
| PC5 | 10 | `10.0.0.4` |
| DB-SRV | 20 | `10.0.0.65` |
| APPL-SRV | 20 | `10.0.0.66` |
| PC3 | 30 | `10.0.0.129` |
| PC4 | 30 | `10.0.0.130` |

The configured SVI addresses serve as the default gateways for their respective subnets.

## 4. Switching Configuration

### 4.1 Access ports

End-device ports are configured as access ports and assigned to the appropriate VLAN.

- SW1 connects workstations in VLANs 10 and 30.
- MLS connects server devices in VLAN 20 and additional workstations in VLAN 10.

Consult the device configuration files for the exact interface assignments.

### 4.2 Trunk link: SW1–MLS

The link between SW1 and MLS carries VLANs 10 and 30 using 802.1Q trunking.

The trunk parameters are:

- Allowed VLANs: `10,30`
- Native VLAN: `1001`

The native VLAN configuration must match at both ends of the trunk.

VLAN 20 is not required on this trunk because the server devices are connected directly to the MLS in this topology.

## 5. Inter-VLAN Routing

The MLS performs Layer 3 routing through its Switch Virtual Interfaces (SVIs).

The configured gateway interfaces are:

- `Vlan10`: `10.0.0.62/26`
- `Vlan20`: `10.0.0.126/26`
- `Vlan30`: `10.0.0.190/26`

When a host sends traffic to a different IP subnet, it forwards the packet to its default gateway. The MLS can then route the packet between the directly connected VLAN networks, subject to its configuration and any applicable policies.

## 6. MLS–R1 Routed Link

The connection between MLS and R1 is a routed Layer 3 link, not a VLAN trunk.

| Device | Interface | IP Address |
|---|---|---|
| MLS | `GigabitEthernet1/0/1` | `10.0.0.193/30` |
| R1 | `GigabitEthernet0/1` | `10.0.0.194/30` |

The transit subnet is `10.0.0.192/30`.

This subnet provides two usable host addresses, one for each device.

## 7. Static Routing

### MLS

The MLS configuration includes a default route pointing to R1:

`ip route 0.0.0.0 0.0.0.0 10.0.0.194`

This tells the MLS to forward traffic for destinations not present in its routing table to R1.

### R1

The R1 configuration includes the following default route:

`ip route 0.0.0.0 0.0.0.0 10.0.0.193`

**Type:** This route points toward the MLS solely to enable connectivity from the outside to the local network; it is not necessarily appropriate for a production environment (where the router should use a dynamic routing protocol to learn the various subnets of the LAN).

The external network shown in the topology is `1.1.1.0/24`, with R1 using `1.1.1.2` on `Serial0/0/0`.

## 8. Verification Procedures

The saved command outputs are available in [`../tests/shows.txt`](../tests/shows.txt).

### VLAN and trunk verification

```text
show vlan brief
show interfaces trunk
```

Use these commands to verify VLAN creation, access-port membership, trunk operation, allowed VLANs, and the native VLAN.

### Interface verification

```text
show ip interface brief
```

Use this command to check the state and IP addressing of the SVIs and routed interface. The saved outputs show the three VLAN interfaces and the MLS routed uplink as up/up.

### Routing-table verification

```text
show ip route
```

Check for:

- Connected routes for the three VLAN subnets.
- The connected MLS–R1 transit network.
- The configured default route.

### End-to-end connectivity

From the end devices, test connectivity to:

1. The local default gateway.
2. A host in another VLAN.
3. A server in VLAN 20.
4. The intended external destination, if external connectivity is part of the test.

For example, on a Packet Tracer PC:

```text
ping 10.0.0.62
ping 10.0.0.126
ping 10.0.0.194
```

Choose the gateway addresses appropriate to the source host and interpret each result in context. A successful ping to a gateway does not establish that all remote destinations are reachable.

## 9. Evidence

The `tests/` directory contains:

- `shows.txt` — captured CLI verification outputs.
- `end-to-end-connectivity.png` — connectivity-test screenshot.
- `simulation-mode.png` — Packet Tracer simulation evidence.

Use these files to assess the observed behavior of the lab alongside the device configurations.

## 10. Configuration Files

| File | Contents |
|---|---|
| `configs/MLS-config.txt` | Multilayer switch configuration |
| `configs/SW1-config.txt` | Layer 2 switch configuration |
| `configs/R1-config.txt` | Edge router configuration |

## 11. Learning Outcomes

This lab reinforces the following networking concepts:

- VLAN creation and access-port assignment.
- 802.1Q trunking and native VLAN configuration.
- Inter-VLAN routing with SVIs.
- Layer 2 versus Layer 3 switch behavior.
- Routed links between network devices.
- Static routes and default-route troubleshooting.
- Verification using Cisco IOS show commands.
- Documentation of network designs and test evidence.