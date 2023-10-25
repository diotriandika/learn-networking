# Security - Part 1 (SSH & Radius Auth)

**Tasks:**

---

- Configure hostnames for all network devices as you see on the topology

- Configure SSH version 2 for remote access on EST-1 and EST-2

  - Use RADIUS server for authentication
    - Use (HQ-SRV or WS-SRV) as RADIUS Server
    - Use Skills39 as the shared key
    - Test RADIUS authentication using following users with password Skills39
      - username user1 with maximum privilege level
      - username user2 with privilege level 5
  - User user 2 should be able to configure any interface IP settings and administratively enable or disable any of these interfaces
  - If RADIUS server goes down, use local account as backup authentication method
  - Ensure only ES-CLI is allowed to access via SSH


---

### Step 1 - Configure 
```
aaa new-model
aaa authen login default group radius local
radius server namaradius
 address 3.0.180.201 auth-port 1812 acct-port 1813
 key Skills39s
```

### Site to site vpn
```kotlin
WST-1
ip route 0.0.0.0 0.0.0.0 3.0.180.254

access-list 100 permit ip 10.0.0.0 0.0.0.255 11.11.11.0 0.0.0.3
access-list 100 permit ip 10.0.0.0 0.0.0.255 12.12.12.0 0.0.0.3

crypto ikev2 proposal 

crypto ikev2 keyring WST-1
 peer EST-1
  address 13.228.27.200
  pre-shared-key local Skills39
  pre-shared-key remote ccie

crypto ikev2 profile IKEv2_Profile
 match identity remote address 13.228.27.200
 authentication local pre-share 
 authentication remote pre-share
 keyring local WST-1
 
crypto ipsec transform-set TSET esp-des esp-md5-hmac

crypto map MYMAP 1 ipsec-isakmp
 set peer 13.228.27.200
 set transform-set TSET
 set ikev2-profile IKEv2_Profile
 match address 100
 
interface gig 0/0
 crypto map MYMAP

verify by run
show ikev2 <ARG>

----

EST-1
ip route 0.0.0.0 0.0.0.0 13.228.27.254

access-list 100 permit ip 11.11.11.0 0.0.0.3 10.0.0.0 0.0.0.255
access-list 100 permit ip 12.12.12.0 0.0.0.3 10.0.0.0 0.0.0.255

crypto ikev2 proposal

crypto ikev2 keyring EST-1
 peer WST-1
  address 3.0.180.201
  pre-shared-key local Skills39
  pre-shared-key remote ccie

crypto ikev2 profile IKEv2_Profile
 match identity remote address 3.0.180.201
 authentication local pre-share
 authentication remote pre-share
 keyring local EST-1

crypto ipsec transform-set TSET esp-des esp-md5-hmac

crypto map MYMAP 1 ipsec-isakmp
 set peer 3.0.180.201
 set transform-set TSET
 set ikev2-profile IKEv2_Profile
 match address 100

check connection, theres should be unavailable
show crypto ipsec sa

interface gig 0/2 
 crypto map MYMAP
```
