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
