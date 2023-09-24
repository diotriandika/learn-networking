# Basic Configuration - Network Systems
**TASK :**

- Configure hostnames fo ALL network devices as you see on the topology
- Configure domain name **lksn2022.id** for ALL network devices
- Configure **"Skills39** as enable secret for ALL network devices
- Create user **admin** with password **"Skills39** from ALL network devices
  - Only encrypted hash of password should be stored in configuration
  - This use should have maximum privileges

## Configuration Steps

> Do this steps in all network devices (R1-R4, ISP, SW1-SW3, FW1)

### Step 1 (Hostname Configuration) 

```mark
router> enable
router# configure terminal
router(config)# hostname <hostname>
```

### Step 2 (Domain Name Configuration)

```markdown
router(config)# ip domain-name lksn2022.id
```

### Step 3 (Enable Secret Password)

```markdown
router(config)# enable secret Skills39
```

### Step 4 (Create User)

```markdown
router(config)# username admin privilege 15 secret Skills39
```

## Examples

**ISP >**

```markdown
router> enable
router# configure terminal
router(config)# hostname ISP
ISP(config)# ip domain-name lksn2022.id
ISP(config)# enable secret Skills39
ISP(config)# username admin privilege 15 secret Skills39
```

**SW3>**

```markdown
router> enable
router# configure terminal
router(config)# hostname SW3
SW3(config)# ip domain-name lksn2022.id
SW3(config)# enable secret Skills39
SW3(config)# username admin privilege 15 secret Skills39
```

## Exception

>  On **ASA/FW1** we can't use a secret command and doesn't require privileges for user creation, but It's already hashed password

```markdown
ciscoasa> enable
ciscoasa# configure terminal
ciscoasa(config)# hostname FW1
FW1(config)# ip domain-name lksn2022.id
FW1(config)# enable password Skills39
FW1(config)# username admin password Skills39