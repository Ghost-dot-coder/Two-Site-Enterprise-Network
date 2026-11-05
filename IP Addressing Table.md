# IP Addressing Table

Two-site network (HQ and Branch) built in Cisco Packet Tracer.
Routing: EIGRP (AS 100). DHCP: R1 serves HQ VLANs, R2 serves the Branch VLAN.

## VLANs and subnets

| VLAN | Name       | Site   | Subnet           | Gateway        | DHCP range                | DHCP server |
|------|------------|--------|------------------|----------------|---------------------------|-------------|
| 10   | HR         | HQ     | 192.168.10.0/24  | 192.168.10.1   | 192.168.10.11 - .254      | R1 (hqRouter) |
| 20   | Sales      | HQ     | 192.168.20.0/24  | 192.168.20.1   | 192.168.20.11 - .254      | R1 (hqRouter) |
| 30   | IT         | HQ     | 192.168.30.0/24  | 192.168.30.1   | 192.168.30.11 - .254      | R1 (hqRouter) |
| 40   | Servers    | HQ     | 192.168.40.0/24  | 192.168.40.1   | Static addressing         | None |
| 50   | Branch     | Branch | 192.168.50.0/24  | 192.168.50.1   | 192.168.50.11 - .254      | R2 (branch) |
| 99   | Management | HQ     | 192.168.99.0/24  | 192.168.99.1   | None (static switch IPs)  | None |

Excluded from DHCP in each pool: .1 - .10 (gateway and reserved addresses).

## Router and switch links

| Link                  | Network          | Side A               | Side B               |
|-----------------------|------------------|----------------------|----------------------|
| R1 to SW1 (HQ)        | 192.168.1.0/24   | R1 Gi0/0: 192.168.1.1 | SW1 Gi0/1: 192.168.1.2 |
| R2 to SW4 (Branch)    | 192.168.2.0/24   | R2 Gi0/0: 192.168.2.1 | SW4 Fa0/24: 192.168.2.2 |
| WAN, R1 to R2         | 10.0.0.0/30      | R1 Gi0/1: 10.0.0.1    | R2 Gi0/1: 10.0.0.2   |

The WAN link uses a /30, which leaves exactly two usable addresses (.1 and .2) for the two routers.

## Management VLAN (VLAN 99)

| Device | Interface | IP address     | Mask            |
|--------|-----------|----------------|-----------------|
| SW1    | Vlan 99   | 192.168.99.1   | 255.255.255.0   |
| SW2    | Vlan 99   | 192.168.99.2   | 255.255.255.0   |
| SW3    | Vlan 99   | 192.168.99.3   | 255.255.255.0   |

Default gateway on SW2 and SW3: 192.168.99.1 (SW1).

## Devices

| Device | Hostname    | Model            | Role                                   |
|--------|-------------|------------------|----------------------------------------|
| R1     | hqRouter    | Cisco 2911       | HQ router, DHCP server, EIGRP          |
| R2     | branch      | Cisco 2911       | Branch router, DHCP server, EIGRP      |
| SW1    | SW1         | Cisco 3560       | HQ core multilayer switch, VLAN gateways |
| SW2    | SW2         | Cisco 2960       | HQ access switch (VLANs 10, 20)        |
| SW3    | SW3         | Cisco 2960       | HQ access switch (VLANs 30, 40)        |
| SW4    | SW4         | Cisco 3560       | Branch multilayer switch (VLAN 50)     |

## Routing

- Protocol: EIGRP, AS 100, `no auto-summary`
- Advertised networks: all VLAN subnets, the two router-to-switch links, and the 10.0.0.0/30 WAN link
- Verification: `show ip eigrp neighbors`, `show ip route` (routes marked `D`)