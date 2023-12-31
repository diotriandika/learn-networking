# Basic EtherChannel Configuration (LACP)

Dalam LACP, agar dapat terbentuk sebiah channel group, salah satunya harus ada yang berperan sebagai Active atau Passive
- Active mode : When the LACP is operating in active mode on either end of a link, both ports can send PDUs. The "active" LACP initiates an LACP connection by sending LACPDUs. The "passive" LACP will wait for the remote end to initiate the link
- Passive Mode : When the LACP is operating in passive mode on a local member port and as its peer port, both ports cannot send PDUs

[Source](https://www.arubanetworks.com/techdocs/AOS-CX/10.11/HTML/link_aggregation/Content/Chp_LAG/lac-ope-mod.htm)

Topologi:

![image-20230926134942074](https://github.com/diotriandika/learn-networking/assets/109568349/24f82b28-b9e4-44c5-96c5-28962cf030f5)


Sebelum dikonfigurasi, terlihat pada topologi diatas bahwa salah satu interface diblock secara otomatis oleh STP agar tidak terjadi frame looping. Agar STP membaca bahwa kedua interface tersebut menjadi 1 interface kita akan melakukan konfigurasi dibawah

## LACP EtherChannel Configuration Steps

### Step 1 (Set Active Switch)

**SW1>**

```markdown
SW1> enable
SW1# configure terminal

# Masuk ke interface yang ingin digabungkan
SW1(config)# interface fastEthernet 0/1-2

# buat channel group dengan ID bebas dan set sebagai active
SW1(config-if-range)# channel-group 1 mode active  
SW2(config-if-range)# exit
```

### Step 2 (Set Passive Switch)

**SW2>**

```markdown
SW2> enable
SW2# configure terminal

# Masuk ke interface yang ingin digabungkan
SW2(config)# interface fastEthernet 0/1-2

# buat channel group dengan ID bebas dan set sebagai active
SW2(config-if-range)# channel-group 1 mode passive
SW2(config-if-range)# exit
```

### Verifikasi

![image-20230926135831260](https://github.com/diotriandika/learn-networking/assets/109568349/c3a693e8-7e85-4195-951b-96fc926bcdd8)

Sekarang kedua interface tersebut statusnya sudah hijau yang menandakan bahwa kedua interface tersebut tidak lagi diblokir oleh protokol STP. Kita bisa cek status dari etherchannel dengan menjalankan perintah

```markdown
<hostname># show etherchannel summary
```

```markdown
SW1#show etherchannel summary
Flags:  D - down        P - in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port


Number of channel-groups in use: 1
Number of aggregators:           1

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------------

1      Po1(SU)           LACP   Fa0/1(P) Fa0/2(P) 
```

```markdown
SW2(config)#do show ether sum
Flags:  D - down        P - in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port


Number of channel-groups in use: 1
Number of aggregators:           1

Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------------------------------------

1      Po1(SU)           LACP   Fa0/1(P) Fa0/2(P)  
```

