# Switching (Part - 2 VLAN)

**Tasks:**

---

- Configure VTP on all switches to synchronize VLANs. It should be possible to modify VLAN database only from L3SW-1. VLAN database on all switches should contain following VLANs.
  - VLAN 10 with name SRV
  - VLAN 20 with name CLI
- Configure all links between switches as trunk port
  - Do not use dynamic negotiation protocol
  - Configure manual pruning so that only created VLANs are allowed forwarding

---

### Step 1 - Create VLAN

Create VLAN on L3SW-1

```kotlin
L3SW-1(config)# vlan 10
L3SW-1(config-vlan)# name SRV
L3SW-1(config-vlan)# vlan 20
L3SW-1(config-vlan)# name CLI
L3SW-1(config-vlan)# exit
```

### Step 2 - Trunking Switches

Configure Trunk between all switches

**L3SW-1:**

```kotlin
// Trunk to L3SW-2
L3SW-1(config)# interface port-channel 1
L3SW-1(config-if)# switchport trunk encapsulation dot1Q
L3SW-1(config-if)# switchport mode trunk
L3SW-1(config-if)# switchport trunk allowed vlan 10,20
L3SW-1(config-if)# exit
// Trunk to L2SW-1
L3SW-1(config)# interface port-channel 2
L3SW-1(config-if)# switchport trunk encapsulation dot1Q
L3SW-1(config-if)# switchport mode trunk
L3SW-1(config-if)# switchport trunk allowed vlan 10,20
L3SW-1(config-if)# exit
// Trunk to L2SW-2
L3SW-1(config)# interface gigabitEthernet 1/1
L3SW-1(config-if)# switchport trunk encapsulation dot1Q
L3SW-1(config-if)# switchport mode trunk
L3SW-1(config-if)# switchport trunk allowed vlan 10,20
L3SW-1(config-if)# exit
```

**L3SW-2:**

```kotlin
// Trunk to L3SW-1
L3SW-2(config)# interface port-channel 1
L3SW-2(config-if)# switchport trunk encapsulation dot1Q
L3SW-2(config-if)# switchport mode trunk
L3SW-2(config-if)# switchport trunk allowed vlan 10,20
L3SW-2(config-if)# exit
// Trunk to L2SW-2
L3SW-2(config)# interface port-channel 3
L3SW-2(config-if)# switchport trunk encapsulation dot1Q
L3SW-2(config-if)# switchport mode trunk
L3SW-2(config-if)# switchport trunk allowed vlan 10,20
L3SW-2(config-if)# exit
// Trunk to L2SW-1
L3SW-2(config)# interface gigabitEthernet 1/1
L3SW-2(config-if)# switchport trunk encapsulation dot1Q
L3SW-2(config-if)# switchport mode trunk
L3SW-2(config-if)# switchport trunk allowed vlan 10,20
L3SW-2(config-if)# exit
```

**L2SW-1:**

```kotlin
// Trunk to L3SW-1
L2SW-1(config)# interface port-channel 2
L2SW-1(config-if)# switchport trunk encapsulation dot1Q
L2SW-1(config-if)# switchprot mode trunk
L2SW-1(config-if)# exit
// Trunk to L3SW-2
L2SW-1(config)# interface gigabitEthernet 1/1
L2SW-1(config-if)# switchport trunk encapsulation dot1Q
L2SW-1(config-if)# switchprot mode trunk
L2SW-1(config-if)# exit
```

**L2SW-2:**

```kotlin
// Trunk to L3SW-2
L2SW-1(config)# interface port-channel 3
L2SW-1(config-if)# switchport trunk encapsulation dot1Q
L2SW-1(config-if)# switchprot mode trunk
L2SW-1(config-if)# exit
// Trunk to L3SW-1
L2SW-1(config)# interface gigabitEthernet 1/1
L2SW-1(config-if)# switchport trunk encapsulation dot1Q
L2SW-1(config-if)# switchprot mode trunk
L2SW-1(config-if)# exit
```

### Step 3 - Configure VTP Server

Configure VTP Server pada **L3SW-1**

```kotlin
// Change mode VTP to Server
L3SW-1(config)# vtp mode server
// Set VTP Domain
L3SW-1(config)# vtp domain lksn2023.id
// Set VTP Password
L3SW-1(config)# vtp password Skills39
```

Configure VTP Client pada **L3SW-2**, **L2SW-1** dan **L2SW-2**

```kotlin
// Change mode VTP to Client
L3SW-2(config)# vtp mode server
// Append Password
L3SW-2(config)# vtp password Skills39
```

Give Access VLAN to Interface Client
```kotlin
// VLAN CLI
L2SW-1(config)# interface gigabitEthernet 0/3
L2SW-1(config-if)# switchport mode access
L2SW-1(config-if)# switchport access vlan 20
// VLAN SRV
L2SW-2(config-if)# interface gigabitEthernet 0/3
L2SW-2(config-if)# switchport mode access
L2SW-2(config-if)# switchport access vlan 10
```
Cek konfigurasi
