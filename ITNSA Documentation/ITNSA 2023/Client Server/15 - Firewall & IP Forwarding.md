 # Firewall and IP Forwarding

**Tasks:**

---

- Configure buton.lks.id with iptables LOG module to log these traffics:
  - Traffic from Malaka Network to Karimata Network
  - Incoming DNS Request
  - Incoming ICMP Request
- Enable IP Forwarding in the required servers to with the following conditions:
  - Lombok Network must be able to reach Karimata Network
  - Karimata Network must be able to reach Lombok Network
  - Malaka Network must not be able to reach Lombok Network without using VPN
  - Malaka Network must not be able to reach Karimata Networki without using VPN
  - Lombok Network must not be able to reach Malaka Network
  - Karimata Network must not be able to reach Malaka Network

---

### Step 1 - Traffic Logging

