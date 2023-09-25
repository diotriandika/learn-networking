# L2 Configuration - Network Systems

**Task:**

![image-20230925074958256](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20230925074958256.png)

## Configuration Steps

### Step 1 (Etherchannel LACP Configuration)

> SW1 as Active and SW2 as Passive

**SW1>**

```markdown
SW1> enable
SW1# configure terminal
SW1(config)# interface range fastEthernet 0/23-24
SW1(config)# channel-group 1 mode active
SW1(config)# exit
```

**SW1>**

```markdown
SW2> enable
SW2# configure terminal
SW2(config)# interface range fastEthernet 0/23-24
SW2(config)# channel-group 1 mode passive
SW2(config)# exit
```

#### Verification

```markdown
<hostname># show etherchannel summary
<hostname># show interface status
```

### Step 2 (Trunk Port)

**SW1>**

```markdown
SW1> enable
SW1# configure terminal
# Etherchannel Port
SW1(config)# interface port-channel 1
SW1(config)# switchport mode trunk
SW1(config)# exit
# FastEthernet 0/4 Port
SW1(config)# interface fastEthernet 0/4
SW1(config)# switchport mode trunk
SW1(config)# exit
# FastEthernet 0/22 Port
SW1(config)# interface fastEthernet 0/22
SW1(config)# switchport mode trunk
SW1(config)# exit
```

**SW2>**

```markdown
SW1> enable
SW1# configure terminal
# Etherchannel Port
SW1(config)# interface port-channel 1
SW1(config)# switchport mode trunk
SW1(config)# exit
# FastEthernet 0/4 Port
SW1(config)# interface fastEthernet 0/4
SW1(config)# switchport mode trunk
SW1(config)# exit
# FastEthernet 0/21 Port
SW1(config)# interface fastEthernet 0/21
SW1(config)# switchport mode trunk
SW1(config)# exit
```

### Step 4 (VTP - VLAN Trunking Protocol)

**SW3>** (VTP Server)

```markdown
SW3> enable
SW3# configure terminal
# Change mode VTP to Server
SW3(config)# vtp mode server
# Change VTP Version
SW3(config)# vtp version 2
# Change VTP Domain
SW3(config)# vtp domain wsc2022.id
# Change VTP Password
SW3> enable
SW3# configure terminalvtp password Skills39
```

**SW2>** (VTP Client)

```markdown
SW2> enable
SW2# configure terminal
# Change mode VTP to Client
SW2(config)# vtp mode client
# Change VTP Domain
SW2(config)# vtp domain wsc2022.id
# Change VTP Password
SW2(config)# vtp password Skills39
```

**SW1>** (VTP Client)

```markdown
SW1> enable
SW1# configure terminal
# Change mode VTP to Client
SW1(config)# vtp mode client
# Change VTP Domain
SW1(config)# vtp domain wsc2022.id
# Change VTP Password
SW1(config)# vtp password Skills39
```

> On Client VTP we don't need to change the version, It's was change automatically after we changed the version on VTP Server

### Step 5 (VLAN)

> We're configured VTP and determine the Server and Client, so here we only creating VLAN table on VTP Server only

#### Creating VLAN

**SW3>**

```markdown
SW3> enable
SW3# configure terminal
# VLAN 10
SW3(config)# vlan 10
SW3(config-vlan)# name HQ10
SW3(config-vlan)# exit
# VLAN 20
SW3(config)# vlan 20
SW3(config-vlan)# name HQ20
SW3(config-vlan)# exit
```

#### VLAN Access

**SW3>** 

```markdown
SW3> enable
SW3# configure terminal
# Interface FastEthernet 0/1 as VLAN 10
SW3(config)# interface fastEthernet 0/1
SW3(config)# switchport access vlan 10
SW3(config)# exit
# Interface FastEthernet 0/2 as VLAN 20
SW3(config)# interface fastEthernet 0/2
SW3(config)# switchport access vlan 20
SW3(config)# exit
```

#### Verification

> Do this on all Switches

```markdown
<hostname># show vlan brief
```

### Step 6 (STP - Spanning Tree Protocol)

**SW1>**

```markdown
SW1> enable
SW1# configure terminal
# VLAN 10 as Primary Root
SW1(config)# spanning-tree vlan 10 root primary
# VLAN 20 as Secondary Root
SW1(config)# spanning-tree vlan 20 root secondary
```

**SW2>**

```markdown
SW2> enable
SW2# configure terminal
# VLAN 20 as Primary Root
SW2(config)# spanning-tree vlan 20 root primary
# VLAN 10 as Secondary Root
SW2(config)# spanning-tree vlan 10 root secondary
```

### Step 7 (BPDU - Bride Protocol Data Unit)

**SW3>**

```markdown
SW3> enable
SW3# configure terminal
# Block Interface that Connected to PC
SW3(config)# interface fastEthernet 0/2
SW3(config)# spanning-tree portfast
SW3(config)# spanning-tree bpduguard enable
SW3(config)# exit
```

