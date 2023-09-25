# Service Configuration - Network Systems

**Task:**

![image-20230925101805008](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20230925101805008.png)

## DHCP Server Configuration Steps

### Step 1 (VLAN20 DHCP Server)

**R3>**

```markdown
R3> enable
R3# configure terminal

# Create Exluded Address
R3(config)# ip dhcp excluded-address 192.168.20.1 192.168.20.10

# Create DHCP Pool VLAN20
R3(config)# ip dhcp pool VLAN20

# Add Network that given to DHCP Pool VLAN20
R3(dhcp-config)# network 192.168.20.0 255.255.255.0

# Set Default Gateway
R3(dhcp-config)# default-router 192.168.20.254
R3(dhcp-config)#exit
```

### Step 2 (BR-CLI DHCP Server)

> In the task, there's no direction to assign specific DHCP Pool name, so here I'm just using BR-CLI as the DHCP Pool name

**R1>**

```markdown
R1> enable
R1# configure terminal

# Create Exluded Address
R1(config)# ip dhcp excluded-address 172.16.10.1 172.16.10.10

# Create DHCP Pool BR-CLI
R1(config)# ip dhcp pool BR-CLI

# Add Network that given to DHCP Pool BR-CLI
R1(dhcp-config)# network 172.16.10.0 255.255.255.0

# Set Default Gateway
R1(dhcp-config)# default-router 172.16.10.254
R1(dhcp-config)#exit
```

### Verification

Check by enabling DHCP Addressing on BR-CLI & HQ-CLI

## PAT Configuration Steps

### Step 1 (Router PAT Configuration)

**R1>** 

```markdown
fR1> enable
R1# configure terminal

# Create Access List with name INTERNET-ACCESS
R1(config)# ip access-list standart INTERNET-ACCESS

# Permit Network Local
R1(config-std-nacl)# permit 172.16.20.0 0.0.0.255
R1(config-std-nacl)# exit
```

```markdown
# Define where as Nat Inside and Nat Outside port

# Gig 0/1 as Nat Inside
R1(config)# interface gigabitEthernet 0/1
R1(config-if)# ip nat inside
R1(config-if)# exit

# Gig 0/0 as Nat Outside
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip nat outside
R1(config-if)# exit
```

```markdown
# Enabling PAT/NAT Overload
R1(config)# ip nat inside source list INTERNET-ACCESS interface gigabitEthernet 0/0 overload
```

**R2>** 

```markdown
R2> enable
R2# configure terminal

# Create Access List with name INTERNET-ACCESS
R2(config)# ip access-list standart INTERNET-ACCESS

# Permit Network Local
R2(config-std-nacl)# permit 192.168.10.0 0.0.0.255
R2(config-std-nacl)# permit 192.168.20.0 0.0.0.255
R2(config-std-nacl)# exit 
```

```markdown
# Define where as Nat Inside and Nat Outside port

# VLAN10 & VLAN20 as Nat Inside
R2(config)# interface gigabitEthernet 0/1.10
R2(config-if)# ip nat inside
R2(config-if)# exit
R2(config)# interface gigabitEthernet 0/1.20
R2(config-if)# ip nat inside
R2(config-if)# exit

# Serial 0/0/1 as Nat Outside
R2(config)# interface serial 0/0/1
R2(config-if)# ip nat outside
R2(config-if)# exit
```

```markdown
# Enabling PAT/NAT Overload
R2(config)# ip nat inside source list INTERNET-ACCESS interface serial 0/0/1 overload
```

**R3>**

```markdown
R3> enable
R3# configure terminal

# Create Access List with name INTERNET-ACCESS
R3(config)# ip access-list standart INTERNET-ACCESS

# Permit Network Local
R3(config-std-nacl)# permit 192.168.10.0 0.0.0.255
R3(config-std-nacl)# permit 192.168.20.0 0.0.0.255
R3(config-std-nacl)# exit 
```

```markdown
# Define where as Nat Inside and Nat Outside port

# VLAN10 & VLAN20 as Nat Inside
R2(config)# interface gigabitEthernet 0/1.10
R2(config-if)# ip nat inside
R2(config-if)# exit
R2(config)# interface gigabitEthernet 0/1.20
R2(config-if)# ip nat inside
R2(config-if)# exit

# Serial 0/0/0 as Nat Outside
R2(config)# interface serial 0/0/0
R2(config-if)# ip nat outside
R2(config-if)# exit
```

```markdown
# Enabling PAT/NAT Overload
R2(config)# ip nat inside source list INTERNET-ACCESS interface serial 0/0/0 overload
```

### Step 2 (ASA PAT Configuration)

**FW1>**

```markdown
FW1> enable
FW# configure terminal

# Create Object Network
FW(config)# object network LOCAL

# Define Network Local
FW(config-network-object)# subnet 172.16.20.0 255.255.255.0

# Enable Translation
FW(config-network-object)# nat (INSIDE,OUTSIDE) dynamic interface
FW(config-network-object)# exit
```

> Check internal name by run
>
> ```markdown
> FW1# show running-config | sec nameif
> ```
>
> 

```markdown
# Create Access List with name INTERNET-ACCESS and permit icmp traffic only
FW1(config)# access-list INTERNET-ACCESS extended permit icmp any any
FW1(config)# access-group INTERNET-ACCESS in interface OUTSIDE
```

### Verification

Check by ping host by host and check which IP are listening.

Also check Network Translation by running

```markdown
<hostname># show ip nat translations
```



## SSH Configuration Steps

### Step 1 (Configure SSHv2 for Remote on ISP)

**ISP>**

```markdown
ISP> enable
ISP# configure terminal

# Activate SSH Version 2
ISP(config)# ip ssh version 2

# Generate Key (optional)
ISP(config)# crypto key generate rsa

# Enable ssh on VTY
ISP(config)# line vty 0 4
ISP(config-line)# transport input SSH

# Enable local user login
ISP(config-line)# login local
ISP(config-line)# exit 
```

**Verify on Remote Terminal**

```powershell
C:\> ssh -l admin 210.103.5.10
```