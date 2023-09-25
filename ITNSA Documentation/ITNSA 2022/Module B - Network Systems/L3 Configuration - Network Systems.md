# L3 Configuration - Network Systems

**Task:**

![image](https://github.com/diotriandika/learn-networking/assets/109568349/f5ad8538-8e95-4d41-a0d2-dfc720dd5b9e)
## Addressing Configuration Steps

> according to Appendix 

### Step 1 (Router Address Configuration)
#### IP Addressing Example (ISP, R1 & R4)

**R1 & R4**

```C++
<hostname>> enable
<hostname># configure terminal

# GigabitEthernet Interface IP Configuration
<hostname>(config)# interface gigabitEthernet <gigabitEthernet-interface>
<hostname>(config-if)# ip address <ip-address> <subnetmask>
<hostname>(config-if)# exit

# Loopback Interface IP Configuration
<hostname>(config)# interface loopback 0
<hostname>(config-if)# ip address <loopback-ip-address> <loopback-subnetmask>
<hostname>(config-if)# exit
```

**ISP**

```C++
<hostname>> enable
<hostname># configure terminal

# GigabitEthernet Interface IP Configuration
<hostname>(config)# interface gigabitEthernet <gigabitEthernet-interface>
<hostname>(config-if)# ip address <ip-address> <subnetmask>
<hostname>(config-if)# exit

# Serial Interface IP Configuration
<hostname>(config)# interface serial <serial-Interfaces>
<hostname>(config-if)# ip address <serial-ip-address> <serial-subnetmask>
<hostname>(config-if)# no shutdown
<hostname>(config-if)# exit

# Loopback Interface IP Configuration
<hostname>(config)# interface loopback 0
<hostname>(config-if)# ip address <loopback-ip-address> <loopback-subnetmask>
<hostname>(config-if)# exit
```

#### VLAN Addressing Example (R2 & R3)
```C++
<hostname>> enable
<hostname># configure terminal
    
# UP Interface that has VLAN
<hostname>(config)# interface gigabitEthernet 0/1
<hostname>(config-if)# no shutdown
<hostname>(config-if)# exit
    
# Adding IP on Sub-interfaces VLAN 10
<hostname>(config)# interface gigabitEthernet0/1.10
<hostname>(config-if)# encapsulation dot1Q 10
<hostname>(config-if)# ip address <vlan10-address> <vlan10-subnetmask>
<hostname>(config-if)# exit
    
# Adding IP on Sub-interface VLAN 20
<hostname>(config)# interface gigabitEthernet0/1.20
<hostname>(config-if)# encapsulation dot1Q
<hostname>(config-if)# ip address <vlan20-address> <vlan20-subnetmask>
<hostname>(config-if)# exit

# Serial Interface IP Configuration
<hostname>(config)# interface serial <serial-Interfaces>
<hostname>(config-if)# ip address <serial-ip-address> <serial-subnetmask>
<hostname>(config-if)# no shutdown
<hostname>(config-if)# exit
    
# Loopback Interface IP Configuration
<hostname>(config)# interface loopback 0
<hostname>(config-if)# ip address <loopback-ip-address> <loopback-subnetmask>
<hostname>(config-if)# exit
```

### Step 2 (VLAN SW IP Address Assign SW1-SW3)
#### Example 

```c++
<hostname>> enable
<hostname># configure terminal
    
# Assigning IP VLAN 10
<hostname>(config)# interface vlan 10
<hostname>(config-if)# ip address <ip-address> <subnet-mask>
<hostname>(config-if)# ipv6 address <ipv6-address>
<hostname>(config-if)# exit
    
# Assigning IP VLAN 20
<hostname>(config)# interface vlan 20
<hostname>(config-if)# ip address <ip-address> <subnet-mask>
<hostname>(config-if)# exit
```

#### Verification

```c++
<hostname># show ip interface brief
<hostname># show running-config | sec vlan
```


### Step 3 (IP Addressing ASA)

**FW1>**

```markdown
FW1> enable
FW1# configure terminal

# GigabitEthernet Interface IP Configuration (Inside)
FW1(config)# interface gigabitEthernet 0/0
FW1(config-if)# nameif INSIDE
FW1(config-if)# ip address <ip-address> <subnetmask>
FW1(config-if)# no shutdown
FW1(config-if)# exit

# GigabitEthernet Interface IP Configuration (Outside)
FW1(config)# interface gigabitEthernet 0/1
FW1(config-if)# nameif OUTSIDE
FW1(config-if)# ip address <ip-address> <subnetmask>
FW1(config-if)# no shutdown
FW1(config-if)# exit

# Loopback Interface IP Configuration
FW1(config)# interface loopback 0
FW1(config-if)# ip address <loopback-ip-address> <loopback-subnetmask>
FW1(config-if)# exit
```

## Dynamic Routing OSPF Configuration Steps

### Step 1 (ISP Interface OSPF)

**ISP>**

```markdown
ISP> enable
ISP# configure terminal

# Enable OSPF with Process ID 5
ISP(config)# router OSPF 5
ISP(config-router)# exit

# Enable Interface OSPF (Gig)
ISP(config)# interface range gigabitEthernet 0/1-2
ISP(config-if)# ip ospf 5 area 0
ISP(config-if)# exit

# Enable Interface OSPF (Serial) do the same thing on Serial 0/0/1
ISP(config)# interface serial 0/0/0
ISP(config-if)# ip ospf 5 area 0
ISP(config-if)# exit
```

### Step 2 (Network OSPF)

**R1>**

```markdown
R1> enable
R1# configure terminal

# Enable OSPF with Process ID 5
R1(config)# router ospf 5

# Add Network
R1(config-router)# network 210.103.5.12 0.0.0.3 area 0
R1(config-router)# network 172.16.10.0 0.0.0.255 area 0

# Add Passive Interface that doesn't connected to other router device
R1(config-router)# passive-interface gigabitEthernet 0/1
R1(config-router)# exit
```

**R2>**

```markdown
R2> enable
R2# configure terminal

# Enable OSPF with Process ID 5
R2(config)# router ospf 5

# Add Network
R2(config-router)# network 210.103.5.4 0.0.0.3 area 0
R2(config-router)# network 210.104.6.0 0.0.0.3 area 0
R2(config-router)# network 192.168.10.0 0.0.0.255 area 0
R2(config-router)# network 192.168.20.0 0.0.0.255 area 0

# Add Passive Interface that doesn't connected to other router device
R2(config-router)# passive-interface gigabitEthernet 0/1
R2(config-router)# exit
```

**R3>** 

```markdown
R3> enable
R3# configure terminal

# Enable OSPF with Process ID 5
R3(config)# router ospf 5

# Add Network
R3(config-router)# network 210.103.5.0 0.0.0.3 area 0
R3(config-router)# network 210.104.6.0 0.0.0.3 area 0
R3(config-router)# network 192.168.10.0 0.0.0.255 area 0
R3(config-router)# network 192.168.20.0 0.0.0.255 area 0

# Add Passive Interface that doesn't connected to other router device
R3(config-router)# passive-interface gigabitEthernet 0/1
R3(config-router)# exit
```

**R4>**

```markdown
R4> enable
R4# configure terminal

# Enable OSPF with Process ID 5
R4(config)# router ospf 5

# Add Network
R4(config-router)# network 210.103.5.8 0.0.0.3 area 0
R4(config-router)# network 210.103.7.0 0.0.0.255 area 0

# Add Passive Interface that doesn't connected to other router device
R4(config-router)# passive-interface gigabitEthernet 0/1
R4(config-router)# exit
```

## HSRP Configuration Steps

### Step 1 (Enable Active Router)

**R3>** 

```markdown
R3> enable
R3# configure terminal
```

```markdown
# HSRP Active on VLAN 10 
R3(config)# interface gigabitEthernet 0/1.10

# Change Version to HSRP Version 2
R3(config-if)# standby version 2

# Add Virtual IP VLAN 10 with Group Number 104
R3(config-if)# standby 104 ip 192.168.10.254

# Set R3 to Active by sets the Group Number 104 Priority to Highest
R3(config-if)# standby 104 priority 255

# Enable preemption
R3(config-if)# standby 104 preempt
R3(config-if)# exit
```

```markdown
# HSRP Active on VLAN 20 
R3(config)# interface gigabitEthernet 0/1.20

# Change Version to HSRP Version 2
R3(config-if)# standby version 2

# Add Virtual IP VLAN 10 with Group Number 20
R3(config-if)# standby 20 ip 192.168.20.254

# Set R3 to Active by sets the Group Number 20 Priority to Highest
R3(config-if)# standby 20 priority 255

# Enable preemption
R3(config-if)# standby 20 preempt
R3(config-if)# exit
```

### Step 2 (Enable Failover Router)

**R2>** 

```markdown
R2> enable
R2# configure terminal
```

```markdown
# HSRP Failover on VLAN 10
R2(config)# interface gigabitEthernet 0/1.10

# Change Version to HSRP Version 2
R2(config-if)# standby version 2

# Add Virtual IP VLAN 10 with Group Number 104
R2(config-if)# standby 104 ip 192.168.10.254
R2(config-if)# exit
```

```markdown
# HSRP Failover on VLAN 20
R2(config)# interface gigabitEthernet 0/1.20

# Change Version to HSRP Version 2
R2(config-if)# standby version 2

# Add Virtual IP VLAN 20 with Group Number 20
R2(config-if)# standby 20 ip 192.168.10.254
R2(config-if)# exit
```

### Verification

Check routing table by run

```markdown
<hostname># show ip route
```

Check connectivity by pinging host by host

Check HSRP information by run

```markdown
<hostname># show standby brief
```

