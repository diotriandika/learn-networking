# Security Configuration - Network Systems

**Task:**

![image-20230925114846259](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20230925114846259.png)

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

