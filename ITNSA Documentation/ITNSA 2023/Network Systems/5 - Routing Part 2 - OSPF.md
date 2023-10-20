# Routing - Part 2 (Default Route & OSPF)

**Tasks:**

---

- Configure default route to ISP on both EST-1 and EST-2
- Configure OSPF
  - Use OSPF area 0 and instance number 11
  - Advertise only Public IP on all routers
  - Advertise the default route to the OSPF neighbour
  - Configure OSPF so that routing updates are not sent into network where they are not required

---

### Step 1 - Configure Default Route

Create Default Route from EST-1 to ISP

```kotlin
EST-1(config)# ip route 0.0.0.0 0.0.0.0 13.228.27.254
```

Create Default Route from EST-2 to ISP

```kotlin
EST-2(config)# ip route 0.0.0.0 0.0.0.0 13.229.28.254
```

### Step 2 - Configure OSPF

Create new OSPF with instance number 11 and backbone area

**ISP:**

```kotlin
ISP(config)# router ospf 11
ISP(config-router)# default-information originate
ISP(config-router)# network 3.0.180.0 0.0.0.255 area 0
ISP(config-router)# network 3.0.190.0 0.0.0.255 area 0
ISP(config-router)# network 13.228.27.0 0.0.0.255 area 0
ISP(config-router)# network 13.229.28.0 0.0.0.255 area 0
ISP(config-router)# passive-interface gigabitEthernet 0/4
ISP(config-router)# exit
```

**WST-1:**

```kotlin
WST-1(config)# router ospf 11
WST-1(config-router)# default-information originate
WST-1(config-router)# network 3.0.180.0 0.0.0.255 area 0
WST-1(config-router)# exit
```

**WST-2:**

```kotlin
WST-2(config)# router ospf 11
WST-2(config-router)# default-information originate
WST-2(config-router)# network 3.0.190.0 0.0.0.255 area 0
WST-2(config-router)# exit
```

**EST-1:**

```kotlin
EST-1(config)# router ospf 11
EST-1(config-router)# default-information originate
EST-1(config-router)# network 13.228.27.0 0.0.0.255 area 0
EST-1(config-router)# network 11.11.11.0 0.0.0.3 area 0
EST-1(config-router)# network 12.12.12.0 0.0.0.3 area 0
EST-1(config-router)# exit
```

**EST-2:**

```kotlin
EST-2(config)# router ospf 11
EST-2(config-router)# default-information originate
EST-2(config-router)# network 13.229.28.0 0.0.0.255 area 0
EST-2(config-router)# network 11.11.11.4 0.0.0.3 area 0
EST-2(config-router)# network 12.12.12.4 0.0.0.3 area 0
EST-2(config-router)# exit
```

**L3SW-1:**

```kotlin
L3SW-1(config)# ip routing
L3SW-1(config)# router ospf 11
L3SW-1(config-router)# default-information originate
L3SW-1(config-router)# network 11.11.11.0 0.0.0.3 area 0
L3SW-1(config-router)# network 12.12.12.4 0.0.0.3 area 0
L3SW-1(config-router)# network 192.168.10.0 0.0.0.255 area 0
L3SW-1(config-router)# network 192.168.20.0 0.0.1.255 area 0
L3SW-1(config-router)# exit
```

**L3SW-2:**

```kotlin
L3SW-2(config)# ip routing
L3SW-2(config)# router ospf 11
L3SW-2(config-router)# default-information originate
L3SW-2(config-router)# network 11.11.11.4 0.0.0.3 area 0
L3SW-2(config-router)# network 12.12.12.0 0.0.0.3 area 0
L3SW-2(config-router)# network 192.168.10.0 0.0.0.255 area 0
L3SW-2(config-router)# network 192.168.20.0 0.0.1.255 area 0
L3SW-2(config-router)# exit
```

