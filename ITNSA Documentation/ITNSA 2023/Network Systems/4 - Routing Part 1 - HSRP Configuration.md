# Routing - Part 1 (Configure FHRP)

**Tasks:**

---

- Configure FHRP on L3SW-1 and L3SW-2	
  - Use Hot Standby Router Protocol v2 for VLAN 10
    - L3SW1 should be used as the default gateway
    - Use 104 as the group number of IPv4k and 106 as the group number of IPv6
    - Use 192.168.10.254 as Virtual IPv4 address and 2001:624C:3201:10::254 as Virtual IPv6 address.
  - Use a Hot Standby Router Protocol v2 for VLAN 10
    - L3SW1 should be used as the default gateway
    - Use 204 as the group number of IPv4k and 206 as the group number of IPv6
    - Use 192.168.20.254 as Virtual IPv4 address and 2001:624C:3201:20::254 as Virtual IPv6 address.

---

### Step 1 - Configure HSRP

Configure **L3SW-1** as active router of **VLAN10** and standby router of **VLAN20**

```kotlin
// Configure HSRP VLAN10
L3SW-1(config)# interface vlan 10
L3SW-1(config-if)# standby version 2
L3SW-1(config-if)# standby 104 ip 192.168.10.254
L3SW-1(config-if)# standby 104 priority 255
L3SW-1(config-if)# standby 104 preempt
L3SW-1(config-if)# exit
// Configure HSRP VLAN20
L3SW-1(config)# interface vlan 20
L3SW-1(config-if)# standby version 2
L3SW-1(config-if)# standby 204 ip 192.168.20.254
L3SW-1(config-if)# exit
```

> Disini saya tidak menambahkan IPv6 seperti pada soal karena pada IP Table tidak terdapat IPv6 untuk switch ini, bahkan semua

Configure L3SW-2 as active router of VLAN20 and standby router of VLAN10

```kotlin
// Configure HSRP VLAN20
L3SW-2(config)# interface vlan 20
L3SW-2(config-if)# standby version 2
L3SW-2(config-if)# standby 204 ip 192.168.20.254
L3SW-2(config-if)# standby 204 priority 255
L3SW-2(config-if)# standby 204 preempt
L3SW-2(config-if)# exit
// Configure HSRP VLAN10
L3SW-2(config)# interface vlan 10
L3SW-2(config-if)# standby version 2
L3SW-2(config-if)# standby 104 ip 192.168.10.254
```

Cek konfigurasi dengan run :

```kotlin
show standby
```

