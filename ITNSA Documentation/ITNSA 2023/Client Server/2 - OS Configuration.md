## OS Configuration

### Step 1 - Create RAID 1 Partition

List Disks

```bash
$ sudo fdisk -l
# or
$ sudo lsblk
```

> here I'll use the `/dev/sdb` & `/dev/sda` disks to create RAID1

Install **mdadm**

```
$ sudo apt-get install mdadm
```

Create new partition on both disks **(Optional - Or Skip to Creating Raid Array)**

```bash
$ sudo fdisk /dev/sdb
```

> n	<-- new partition
>
> p	<-- primary type
>
> 1	<--- partition number
>
> enter	<--- start sector (default)
>
> enter	<--- end sector (default)
>
> w	<-- write and exit

Do again with `/dev/sdc`

Create **RAID 1 Array** using **mdadm**

```bash
mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
```

> enter `y` to continue

Check with

```bash
$ sudo mdadm --detail /dev/md0
$ cat /proc/mdstat
```

Format Disks to EXT4

```bash
$ sudo mkfs.ext4 /dev/md0
```

### Step 2 - Mounting Disk

Create new directory

```bash
$ sudo mkdir /data  # make sure in root directory
```

Mount disk to `/data`

```bash
$ sudo mount /dev/md0 /data
```

Configure `/etc/fstab`

```bash
$ sudo nano /etc/fstab
---
# Add in the end of line
/dev/md0	/data	ext4	defaults	0	0
```

Try to **reboot** the system

Reference : 

- https://www.linuxbabe.com/linux-server/linux-software-raid-1-setup
- https://www.digitalocean.com/community/tutorials/how-to-create-raid-arrays-with-mdadm-on-debian-9
- https://www.cherryservers.com/blog/how-to-partition-and-format-disk-drives-on-linux

### Step 3 - Sudo Privilege SSH

Create user for **sudo** privilege over **SSH** (Optional)

```bash
$ sudo adduser competitor
```

> user competitor
>
> passwd Skills39

Add competitor to **sudo** group

```bash
$ sudo usermod -aG sudo competitor
```

Restrict **ROOT** login over **SSH**

```bash
$ sudo nano -l /etc/ssh/sshd_conf
---
# Uncomment Line 34 and update the following changes
PermitLoginRoot no
# Under the PermitLoginRoot we can Add new Line to allow specific user to use (Optional)
AllowUsers competitor
```

Restart SSH

```bash
$ sudo systemctl restart sshd
```

## 
