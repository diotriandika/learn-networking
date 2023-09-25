# Security Configuration - Network Systems

**Task:**

![image](https://github.com/diotriandika/learn-networking/assets/109568349/d145a028-b460-4304-ad29-8265681cd48b)

## ASA Firewall Configuration Steps

### Step 1 

**FW1>**

```markdown
FW1> enable
FW1# configure terminal

# Set Outside Interface with 0 Security Level
FW1(config)# interface gigabitEthernet 1/1
FW1(config-if)# security-level 0
FW1(config-if)# exit

# Set Inside Interface with 100 Security Level
FW1(config)# interface gigabitEthernet 1/2 
FW1(config-if)# security-level 100
FW1(config-if)# exit
```

### Verification
Check by pinging host by host
Also check the Nat Translation by running
```markdown
FW1# show ip nat translations
```
