# Basic Configuration

**Tasks:** 

---

- Configure hostnames for all network devices as you see on the topology
- Configure domain name lksn2023.id for all network devices on the topology
- Configure Skills39 as a privileged mode password for all devices
- Only PBKDF2 hash of the password should be stored in configuration
- Configure IPv4/IPv6 address for all network devices as you see on the topology.
- Configure GMT+7 as a timezone for all network devices

---

### Step 1 - Set Hostname

Akses semua Network Device dan masuk ke global configuration mode

```kotlin
changeme> enable
changeme# configure terminal
```

Ganti hostname, contoh disini saya mengganti hostname ISP, sesuaikan hostname device yang dikonfigurasi dengan Configuration Table

```bash
changeme(config)# hostname ISP
```

### Step 2 - Configure Domain Name

Akses ke semua Network Device, set default domain 

```kotlin
ISP(config)# ip domain-name lksn2023.id
```

### Step 3 - Configure Privileged Mode Password

Akses ke semua Network Devices, set Privileged Mode Password

```kotlin
ISP(config)# enable secret Skills39
```

### Step 4 - Storing PBKDF2 Password

Pake radius server buat auth passnya dengah hash PBDKF2

![image-20231019081150352](https://github.com/diotriandika/learn-networking/assets/109568349/ac4cbb74-53a4-4df4-8cf5-4a2cb80cb34e)

### Step 5 -  IP Addressing Network Devices

Akses Network Device, masuk ke interface yang akan dikonfigurasi, dibawah hanya contoh

**Router:**

```kotlin
ISP(config)# interface gigabitEthernet 0/0
ISP(config-if)# ip address 3.0.180.254 255.255.255.0
ISP(config-if)# no shutdown
ISP(config-if)# exit
```

**Layer 3 Switch:**

```kotlin
L3SW-1(config)# interface gigabitEthernet 0/0
L3SW-1(config-if)# no switchport
L3SW-1(config-if)# ip add 11.11.11.1 255.255.255.252
L3SW-1(config-if)# no shutdown
L3SW-1(config-if)# exit
// Configure VLAN IP Address
L3SW-1(config)# interface vlan 10
L3SW-1(config-if)# ip address 192.168.10.253 255.255.255.0
L3SW-1(config-if)# no shutdown
L3SW-1(config-if)# exit
```

**Layer 2 Switch:**

```kotlin
L2SW-1(config)# interface vlan 10
L2SW-1(config-if)# ip address 192.168.10.100 255.255.255.0
L2SW-1(config-if)# no shutdown
L2SW-1(config-if)# exit
```

> Ada kesalahan dalam pembuatan soal, karena pada soal tertera untuk mengkonfigurasi IPv6 akan tetapi pada Configuration Table tidak terdapat satupun Device yang menggunakan IPv6
>
> Namun jika kalanya muncul, tambahkan parameter tambahan berikut
>
> ```kotlin
> ISP(config-if)# ipv6 address xxxx
> ```

### Step 6 - Configure Timezone

Akses Network Device, dan set Timezone ke GMT+7

```kotlin
ISP(config)# clock timezone GMT+7 7
```







