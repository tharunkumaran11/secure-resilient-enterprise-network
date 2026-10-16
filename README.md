# Secure and Resilient Enterprise Network

A Cisco Packet Tracer project demonstrating segmented departmental networks, inter-VLAN routing, OSPF, extended ACLs, Spanning Tree Protocol (STP), and routed-link failover.

## Project objectives

- Separate departments using VLANs.
- Route between VLANs using router-on-a-stick.
- Exchange routes between R1 and R2 using OSPF.
- Apply an ACL intended to restrict traffic between HR and Finance.
- Demonstrate a serial backup route when the primary routed link is disabled.
- Use STP to prevent Layer 2 loops across redundant switch links.

## Topology and devices

- Routers: `R1`, `R2`
- Switches: `SW-Access`, `SW-Core1`, `SW-Core2`
- End devices: `PC-HR`, `PC-Finance`, `PC-IT`, `PC-Remote`
- Server: `SRV-Internal`

See [Screenshots/01_network_topology.png](Screenshots/01_network_topology.png).

## VLAN and IP addressing plan

| VLAN | Purpose | Network | Gateway |
|---:|---|---|---|
| 10 | HR | `192.168.10.0/24` | `192.168.10.1` |
| 20 | Finance | `192.168.20.0/24` | `192.168.20.1` |
| 30 | IT | `192.168.30.0/24` | `192.168.30.1` |
| 40 | Internal server | `192.168.40.0/24` | `192.168.40.1` |
| 50 | Remote network | `192.168.50.0/24` | `192.168.50.1` |

The intended internal server address is `192.168.40.10`.

## Routing and redundancy

### Primary routed link
- R1 `GigabitEthernet0/1`: `10.0.0.1`
- R2 `GigabitEthernet0/1`: `10.0.0.2`

### Serial backup link
- R1 `Serial0/1/0`: `10.0.0.5/30`
- R2 `Serial0/1/0`: `10.0.0.6/30`

OSPF process 1 advertises the routed networks. During the captured failover test, R1's primary `GigabitEthernet0/1` was shut down and the route to `192.168.50.0/24` changed to the serial backup next hop `10.0.0.6` through `Serial0/1/0`. The primary interface was subsequently brought back up.

See:
- [Screenshots/03_ospf_neighbors_and_acl.png](Screenshots/03_ospf_neighbors_and_acl.png)
- [Screenshots/05_failover_to_serial_backup.png](Screenshots/05_failover_to_serial_backup.png)
- [Screenshots/06_primary_link_restored.png](Screenshots/06_primary_link_restored.png)

## Layer 2 redundancy

Two switch-to-switch links connect `SW-Core1` and `SW-Core2`. STP identifies root/designated ports and prevents a Layer 2 loop by blocking a redundant path where necessary. An orange indicator on a redundant switch link can be normal when STP has placed that port into a blocking state.

See [Screenshots/04_stp_redundancy.png](Screenshots/04_stp_redundancy.png).

## ACL security policy

ACL 110 includes these rules:

```text
deny ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255
deny ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
permit ip any any
```

The intended policy is to block IP traffic between HR and Finance while permitting other IP traffic. The ACL entries are visible in the evidence, but the screenshots in this repository do not conclusively demonstrate successful end-to-end HR–Finance blocking. Verify this behavior separately before claiming the isolation test passed.

## Connectivity evidence

The PC-HR command prompt screenshot shows successful pings to the internal server `192.168.40.10` with 0% packet loss. A ping to `192.168.30.21` also received replies after one initial timeout.

See [Screenshots/07_hr_to_server_connectivity.png](Screenshots/07_hr_to_server_connectivity.png).

## Verification commands

```text
show vlan brief
show interfaces trunk
show spanning-tree vlan 10
show ip interface brief
show ip ospf neighbor
show ip route ospf
show access-lists 110
```

## Repository contents

- `README.md` — project overview and documentation
- `ospf-redundancy-config.txt` — OSPF and backup-link configuration reference
- `vlan-acl-config.txt` — VLAN, trunk and ACL configuration reference
- `test-results.md` — test procedure and evidence-based results
- `router-on-a-stick-example.txt` — subinterface addressing example
- `.gitignore` — common local/temporary files to ignore
- `Screenshots/` — selected screenshots from the project

## Technologies

Cisco Packet Tracer · VLANs · 802.1Q trunking · Router-on-a-stick · OSPF · Extended ACLs · STP · Serial backup routing
