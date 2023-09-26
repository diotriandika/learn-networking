# Basic HSRP Configuration (Router)

**HSRP** atau **Hot Standby Router Protocol** adalah protokol redudancy milik Cisco yang menetapkan sebuah router secara otomatis untuk menagmbil alih jika router yang lain gagal, dalam hal ini Router didefinisikan menjadi dua status, yaitu **Active** dan **Standby**. Router standby digunakan sebagai redudancy dari router active jika router active gagal merouting atau bahkan down. HSRP dirancang untuk memungkinkan failover secara transparan.

 Salah satu fitur dari HSRP yaitu dapat dikonfigurasi untuk menyediakan Layer 3 redundansi untuk network host. Dua router interface bekerja sama untuk menyajikan 1 virtual router atau default gateway untuk host di LAN, jadi ketika salah satu router down yang di konfigurasi HSRP link pada jaringan tersebut akan tetap berjalan, di karenakan ip gateway yang di kenal host adalah **virtual router**.

**Topologi:** 

![image-20230926152523634](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20230926152523634.png)

**Addressing Table:**

| Device  | IP Address / Prefix | Interface | Description |
| ------- | ------------------- | --------- | ----------- |
| Router1 | 192.168.10.1/24     | Gig 0/1   | CLT LAN     |
|         | 172.16.65.1/24      | Gig 0/2   | SRV LAN     |
|         | 10.10.10.1/30       | Gig 0/0   | Failover    |
| Router2 | 192.168.10.2/24     | Gig 0/1   | CLT LAN     |
|         | 172.16.65.2/24      | Gig 0/2   | SRV LAN     |
|         | 10.10.10.2/30       | Gig 0/0   | Failover    |
| CLT     | 192.168.10.5        | Fa0       |             |
| SRV     | 172.16.65.5         | Fa0       |             |

## HSRP Configuration Steps

### Step 1 (Router Addressing)

**Router1>**

```markdown
Router1> enable
Router1# configure terminal

# Konfigurasi IP Address (to CLT)
Router1(config)# interface gigabitEthernet 0/1
Router1(config-if)# ip address 192.168.10.1 255.255.255.0
Router1(config-if)# no shutdown
Router1(config-if)# exit

# Konfigurasi IP Address (to CLT)
Router1(config)# interface gigabitEthernet 0/2
Router1(config-if)# ip address 172.16.65.1 255.255.255.0
Router1(config-if)# no shutdown
Router1(config-if)# exit

# Konfigurasi IP Address (to Router2)
Router1(config)# interface gigabitEthernet 0/1
Router1(config-if)# ip address 10.10.10.1 255.255.255.252
Router1(config-if)# no shutdown
Router1(config-if)# exit
```

**Router2>**

```markdown
Router2> enable
Router2# configure terminal

# Konfigurasi IP Address (to CLT)
Router2(config)# interface gigabitEthernet 0/1
Router2(config-if)# ip address 192.168.10.2 255.255.255.0
Router2(config-if)# no shutdown
Router2(config-if)# exit

# Konfigurasi IP Address (to CLT)
Router2(config)# interface gigabitEthernet 0/2
Router2(config-if)# ip address 172.16.65.2 255.255.255.0
Router2(config-if)# no shutdown
Router2(config-if)# exit

# Konfigurasi IP Address (to Router2)
Router2(config)# interface gigabitEthernet 0/1
Router2(config-if)# ip address 10.10.10.2 255.255.255.252
Router2(config-if)# no shutdown
Router2(config-if)# exit
```

### Step 2 (Enable HSRP Active Router)

**Router1>**

```markdown
# Masuk ke interface yang ingin diberikan IP Virtual / Virtual Router
Router1(config)# interface gigabitEthernet 0/1

# Rubah versi HSRP ke Versi 2
Router2(config-if)# standby version 2

# Tambahkan Virtual IP untuk CLT (10 disini bisa dirubah sesuai keinginan, ini adalah identifier untuk grouping)
Router2(config-if)# standby 10 ip 192.168.10.254

# Set priority ke tertinggi agar menjadi Active Router
Router2(config-if)# standby 10 priority 255

# Aktifkan preemption (Berfungsi sebagai pengembalian gateway jika Active Router Alive kembali)
Router2(config-if)# standby 10 preempt
Router2(config-if)# exit
```

### Step 3 (Enable HSRP Standby Router)

```markdown
# Masuk ke interface yang ingin dijadikan sebagai Standby Router
Router2(config-if)# interface gigabitEthernet 0/1

# Rubah versi HSRP ke versi 2
Router2(config-if)# standby version 2

# Tambahkan Virtual IP dan samakan Identifier Group dengan Active Router
Router2(config-if)# standby 10 ip 192.168.10.254
Router2(config-if)# exit
```

### Verfikasi

```markdown
<hostname># show standby brief
```

Coba gunakan IP Virtual tersebut sebagai default gateway pada CLT, lalu ping ke SRV

Selanjutnya coba untuk mematikan Router1 dan cek status pada Router2 dengan 

```markdown
Router2# show standby brief
```

jika statusnya berubah menjadi

```markdown
State is Active
```

Coba untuk ping lagi dari CLT ke SRV