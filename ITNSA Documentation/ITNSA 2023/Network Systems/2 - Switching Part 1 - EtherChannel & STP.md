# Switching (Part 1 - EtherChannel & STP)

**Tasks:** 

- Configure EtherChannel between switches.
  - Use following port-channel numbers
    - 1 - between L3SW-1 and L3SW-2
    - 2 - between L3SW-1 and L2SW-1
    - 3 - between L3SW-2 and L2SW-2
  - The aggregated channel between L3SW-1 and L3SW-2 do not use dynamic negotiation protocol
  - The aggregated channel between L3SW-1 and L2SW-1 use a Cisco proprietary protocol for dynamic negotiation.
  - The aggreagted channel between L3SW-2 and L2SW-2 use a standart procotol for dynamic negotiation
  - L3SW-1 and L3SW-2 should initiate negotiation and the other devices should respond but don't initiate.
  - Configure the load balancing and forwarding method with source and destination MAC address.
- Spanning Tree configuration
  - L3SW-1 should be root bridge of VLAN10. if L3SW-1 goes down, L3SW-2 should take over as the root bridge
  -  should be root bridge of VLAN20. if L3SW-2 goes down, L3SW-1 should take over as the root bridge
  - The traffic from ES-CLI should pass through L3SW-1 (FHRP here)
  - Configure port which is connected to end device so that it immediately begins forwarding when connected

---

### Step 1 - Configure EtherChannel

Create port-channel between L3SW-1 and L3SW-2 **(no dynamic negotiation)**

**L3SW-1:**

```kotlin
L3SW-1(config)# interface gigabitEthernet 1/0
L3SW-1(config-if)# channel-group 1 mode on
L3SW-1(config-if)# exit
L3SW-1(config)# interface gigabitEthernet 0/3
L3SW-1(config-if)# channel-group 1 mode on
L3SW-1(config-if)# exit
```

**L3SW-2:**

```kotlin
L3SW-2(config)# interface gigabitEthernet 1/0
L3SW-2(config-if)# channel-group 1 mode on
L3SW-2(config-if)# exit
L3SW-2(config)# interface gigabitEthernet 0/3
L3SW-2(config-if)# channel-group 1 mode on
L3SW-2(config-if)# exit
```

Create port-channel between L3SW-1 and L2SW-1 **(PAgP)**

**L3SW-1:**

```kotlin
L3SW-1(config)# interface range gigabitEthernet 0/1-2
L3SW-1(config-if)# channel-group 2 mode desirable
L3SW-1(config-if)# exit
```

**L2SW-1:**

```kotlin
L2SW-1(config)# interface range gigabitEthernet 0/1-2
L2SW-1(config-if)# channel-group 2 mode auto
L2SW-1(config-if)# exit
```

Create port-channel between L3SW-2 and L2SW-2 **(LACP)**

**L3SW-2:**

```kotlin
L3SW-2(config)# interface range gigabitEthernet 0/1-2
L3SW-2(config-if)# channel-group 3 mode active
L3SW-2(config-if)# exit
```

**L2SW-2:**

```kotlin
L2SW-2(config)# interface range gigabitEthernet 0/1-2
L2SW-2(config-if)# channel-group 3 mode passive
L2SW-2(config-if)# exit
```

Set loadbalancing and forwarding method to use source and destination mac address

**do on all switches**

```kotlin
port-channel load-balance src-dst-mac
```

### Step 2 - Configure STP 

L3SW-1 as Root Bridge of VLAN10 & Secondary Bridge of VLAN20

**L3SW-1:**

```kotlin
L3SW-1(config)# spanning-tree vlan 10 root primary
L3SW-1(config)# spanning-tree vlan 20 root secondary
```

L3SW-2 as Root Bridge of VLAN20 & Secondary Bridge of VLAN10

**L3SW-2:**

```kotlin
L3SW-2(config)# spanning-tree vlan 20 root primary
L3SW-2(config)# spanning-tree vlan 10 root secondary
```

Configure PortFast to immediately begins forwarding when connected

**L2SW-1:**

```kotlin
L2SW-1(config)# interface gigabitEthernet 0/3
L2SW-1(config-if)# spanning-tree portfast
L2SW-1(config-if)# exit
```

**L2SW-2:**

```kotlin
L2SW-2(config)# interface gigabitEthernet 0/3
L2SW-2(config-if)# spanning-tree portfast
L2SW-2(config-if)# exit
```

