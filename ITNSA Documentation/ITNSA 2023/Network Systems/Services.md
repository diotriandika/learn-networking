# Services

**Tasks:**

---

- Configure DHCP
  - ES-CLI can obtain IP Address automatically.
  - ALL DHCP client should use WS-SRV as DNS-Server
- Configure NAT
  - When ES-CLI communicate with internet, these IP Address should be translated to 103.76.14.1 - 103.76.14.10
- Configure remote monitoring using SNMP
  - Configure device location Surabaya, Indonesia
  - Configure system contact admin@lksn2023.id
  - Cacti monitoring server is pre-configured on ES-SRV, you can use it to check weather SNMP is working correctly or not via http://192.168.10.1/cacti (username: admin, password:Skills39)
  - In Cacti, you must to configure templates for EST-1 and EST-2
- Configure ISP as NTP Server. All network devices should sychronize time from ISP

---

### Step 1 - Configure DHCP

Create pool dhcp in **L3SW-1 & L3SW-2**

```kotlin
L3SW-1(config)# ip dhcp pool VLANCLI
L3SW-1(dhcp-config)# network 192.168.20.0 255.255.254.0
L3SW-1(dhcp-config)# dns-server 10.0.0.101
L3SW-1(dhcp-config)# default-router 192.168.20.254
L3SW-1(dhcp-config)# exit
```

Check on client

### Step 2 - Configure Dynamic NAT

Define nat inside and outside

```kotlin
// Interface Gig 0/0-1 as Nat Inside 
EST-1(config)# interface gigabitEthernet 0/0
EST-1(config-if)# ip nat inside
EST-1(config-if)# exit
EST-1(config)# interface gigabitEthernet 0/1
EST-1(config-if)# ip nat inside
EST-1(config-if)# exit
// Interface Gig 0/2 as Nat Outside
EST-1(config)# interface gigabitEthernet 0/2
EST-1(config-if)# ip nat outside
EST-1(config-if)# exit
```

Create Nat Pool

```kotlin
EST-1(config)# ip nat pool CLIPOOL 13.228.27.1 - 13.228.27.10 netmask 255.255.255.0
```

Create standart Access-List

```kotlin
EST-1(config)# access list 10 permit 192.168.20.0 0.0.1.255
```

Enable Dynamic Nat

```kotlin
EST-1(config)# ip nat inside source list 10 pool CLIPOOL
```

### Step 3 - Configure Remote Monitoring (SNMP)

Configure SNMP on EST-1 & EST-2

```kotlin
EST-1(config)# snmp-server community LKS RO
EST-1(config)# snmp-server location Surabaya, Indonesia
EST-1(config)# snmp-server contact admin@lksn2023.id
```

Do the same configuration on EST-2

Verify SNMP Configuration

```kotlin
show snmp
```

### Step 4 - Configure Cisco Device Templates (Cacti)

Login cacti with cred :

> Username : admin
>
> Password : Skills39

Add EST-1 and EST-2 to Cacti. 

```kotlin
Console > Management > Devices > Add
```

![image-20231022011601101](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231022011601101.png)

Configure with following changes

![image-20231022012121615](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231022012121615.png)

If done select create button

![image-20231022012205401](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231022012205401.png)

Next Select `Create Graphs for this Device`

![image-20231022012316952](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231022012316952.png)

Check for `Graph Template Names` and `Data Query` then change the `Graph Type` to `In/Out Bits (64)` and Create + OK

![image-20231022012556539](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231022012556539.png)

Configure Cacti Tree

```kotlin
Console > Management > Trees > Add Tree (Plus Icon)
```

![image-20231022013023625](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231022013023625.png)

Add name then create

![image-20231022013114679](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231022013114679.png)

Next Select Edit Tree

![image-20231022013311711](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231022013311711.png)

Check on Publish Button

![image-20231022013335738](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231022013335738.png)

Hold and Drag EST-1 from `Available Device` to left pane `Tree Items` then save

![image-20231022013443370](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231022013443370.png)

**Do the same steps to configure EST-2 monitoring**

Check on Graph tab

![image-20231022013810759](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231022013810759.png)

> Reference for cacti Installation : 
>
> - https://ewams.net/?date=2014/07/14&view=How_to_install_Cacti_on_Debian_7_Step_by_Step
> - https://techexpert.tips/cacti/cacti-network-monitor-cisco-switch/

### Step 5 - Configure NTP Server

Open ISP Router

