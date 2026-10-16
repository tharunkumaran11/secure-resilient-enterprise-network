# Test Results — Secure and Resilient Enterprise Network

## 1. OSPF neighbour formation
**Evidence:** `Screenshots/03_ospf_neighbors_and_acl.png`

- OSPF neighbour was shown in FULL state over `Serial0/1/0`.
- OSPF neighbour was shown in FULL/DR state over `GigabitEthernet0/1` before the primary-link test.

## 2. Primary-link failover
**Evidence:** `Screenshots/05_failover_to_serial_backup.png`

Procedure:
1. On R1, enter configuration mode.
2. Shut down `GigabitEthernet0/1`.
3. Run `show ip route ospf`.

Captured result:
- Route to `192.168.50.0/24` used next hop `10.0.0.6` through `Serial0/1/0`.
- This is evidence that OSPF selected the serial backup path when the primary routed link was down.

## 3. Primary-link restoration
**Evidence:** `Screenshots/06_primary_link_restored.png`

- `GigabitEthernet0/1` was brought back up with `no shutdown`.
- The OSPF neighbour table showed the serial neighbour FULL and the primary-link neighbour transitioning through INIT/2WAY while it reconverged.
- Allow convergence and run `show ip ospf neighbor` again if a final FULL state screenshot is needed.

## 4. Server connectivity
**Evidence:** `Screenshots/07_hr_to_server_connectivity.png`

- PC-HR pinged `192.168.40.10`.
- Result: 4 sent, 4 received, 0% packet loss.

## 5. HR–Finance ACL isolation
**Status:** Configuration entries are present, but the included screenshots do not prove the final end-to-end test.
- Expected: traffic between `192.168.10.0/24` and `192.168.20.0/24` is denied.
- Expected: other traffic is permitted by the final `permit ip any any` entry.
- Verify from both departments with `ping` to the other department's host, and inspect `show access-lists 110` match counters.

## 6. STP
**Evidence:** `Screenshots/04_stp_redundancy.png`

- `show spanning-tree vlan 10` lists a root port and designated forwarding ports.
- The two links between core switches provide physical path redundancy; STP prevents a loop by blocking a redundant path as needed.
