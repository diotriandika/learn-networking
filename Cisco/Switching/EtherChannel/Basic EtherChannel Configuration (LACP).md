# Basic EtherChannel Configuration (LACP)

Dalam LACP, agar dapat terbentuk sebiah channel group, salah satunya harus ada yang berperan sebagai Active atau Passive

Topologi:

![image-20230926134942074](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20230926134942074.png)

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

![image-20230926135831260](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20230926135831260.png)

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

