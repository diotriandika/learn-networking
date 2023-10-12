## Additional Storage

**Tasks:** 

> Configure muna.lks.id disk to be shared via iSCSI
>
> - Share two disks that is not used by RAID or the OS.
> - Make sure disk is accessible by jukung.lks.id and komodo.lks.id

### Step 1 - Configuring iSCSI Target

Install **tgt**

```bash
$ sudo apt install tgt
```

Check available disk

```bash
$ sudo lsblk
```

> Here I'm using the `/dev/sdd` & `/dev/sde` disk for the target disks of iscsi

Create **iSCSI Target** configuration file

```bash
$ sudo nano /etc/tgt/conf.d/iscsi.conf
---
<target iqn.2023-10.id.lks.muna:disk1>
	backing-store /dev/sdd
	initiator-address 10.196.10.10
	initiator-address 10.196.10.11
</target>

<target iqn.2023-10.id.lks.muna:disk2>
	backing-store /dev/sde
	initiator-address 10.196.10.10
	initiator-address 10.196.10.11
</target>
```

Restart **tgt** 

```bash
$ sudo systemctl restart tgt
```

Check iscsi disks

```bash
$ sudo tgtadm --mode target --op show
```

Reference :

- https://www.tecmint.com/setup-iscsi-target-and-initiator-on-debian-9/

- https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/ch-iscsi

### Step 2 - Configure iSCSI Initiator

**Tasks:**

> Setup iSCSI in jukung.lks.id and komodo.lks.id to access the previous disk.
>
> - There are two disks, one for jukung.lks.id and one for komodo.lks.id.
> - Mount the disk at the same F: drive using suitable filesystem

Go to Jukung & Komodo Server, open Server Manager and add new Roles & Features

```kotlin
Installation Type > Role based
Server Selection > <jukung/komodo>
Server Roles > File and Storage Services > File and iSCSI Services > iSCSI Target Server
Features > Skip
Confirmation > Install
```

![image-20231012084901645](https://github.com/diotriandika/learn-networking/assets/109568349/b2a04b1e-a941-4d75-9a0c-4435251aed44)


After installed, open **Server Manager**, select **Tools** section then select **iSCSI Initiator** 

> If pop-up window showing, select **Yes**

In Target section enter `muna.lks.id` ipv4 addresses and select **Quick Connect**

![image-20231012085840783](https://github.com/diotriandika/learn-networking/assets/109568349/11a2eb67-191c-43f2-8949-bb63bc9b8a42)

The new window will showing up, Select the disk that we'll used then click **Connect** untul the **Status** changed to **Connected**. Exit by select **Done** menu

Now open **Disk Management** to use the disk. Open **Windows Search** and search for **Disk Management** then Enter

Right-Click on **Disk 1** which is the Shared Disk (indicated by offline status or unallocated while unconfigured), online the disk by select **Online**

![image-20231012091416407](https://github.com/diotriandika/learn-networking/assets/109568349/320fc6a4-03cf-45a9-b252-c1abb46e9e80)

Initialize the disk by right-click again on **Disk 1** and select **Initialize Disk** and use **GPT** or **MBR** 

Next allocate the disk by Right-Click on the disk then select **New Simple Volume**

![image-20231012091718942](https://github.com/diotriandika/learn-networking/assets/109568349/75abd7f1-a04e-40dc-a30f-75b4e4c331c6)

Follow the Wizzard and make sure to choose **F:** for the disk letter

### Step 3 - Creating & Sharing Folder

**Tasks:**

> Share these folders to be able to read-write anonymously:
>
> - F:\backup at jukung.lks.id
> - F:\backup at komodo.lks.id
> - C:\backup at jukung.lks.id
>
> Create the folder if it does not exist.

Open **Windows Explorer** then create folder according to the task, then share the folder with allowing r-w anonymously.
