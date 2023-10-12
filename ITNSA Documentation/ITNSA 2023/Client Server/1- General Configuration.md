## General Configuration

### Step 1 - Configure IP Address

> Konfigurasi IP Address sesuai dengan Addressing Table

### Step 2 - Set Hostname

**Linux:**

```bash
# hostname sesuaikan dengan node yang dikonfigurasi (sebagai contoh untuk node muna)
$ sudo hostnamectl set-hostname muna.lks.id

# Edit file hosts dan tambahkan hostname
$ sudo nano /etc/hosts
---
127.0.0.1       localhost
127.0.1.1       muna.lks.id     muna

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
---
```

**Windows:**

Search **About** in **System Settings** and select **Rename this PC**

![image-20231011195811248](https://github.com/diotriandika/learn-networking/assets/109568349/63c913f4-cf5b-4e31-88bb-e1b9ccedbc79)

**Or** in Windows Server, Open **Server Manager** and select **Local Server** section then right-click on the Current Computer Name (Blue Highlighted Text). Next Select **Change** on Pop-Up Window.

![image-20231011200401844](https://github.com/diotriandika/learn-networking/assets/109568349/45a3d3f0-c446-4f91-8d50-a3e186d1e261)


![image-20231011200444633](https://github.com/diotriandika/learn-networking/assets/109568349/f0c2bcba-6540-4f11-b410-f1c5da69b425)


**Reboot** 

### Step 3 - Allow ICMP Firewall

Open **PowerShell** run:

```powershell
netsh advfirewall firewall add rule name="ICMP Allow incoming V4 echo request" protocol="icmpv4:8,any" dir=in action=allow
```

### Step 4 - Configure SSH (Disable Root Login)

Install **OpenSSH**

```bash
$ sudo apt-get install openssh-server -y
```

Edit **SSH** configuration file

```bash
$ sudo nano -l /etc/ssh/sshd_conf
---
# Uncomment Line 34 and update the following changes
PermitLoginRoot no
```

Restart **SSH** service

```bash
$ sudo systemctl restart sshd
```

## 
