## Lombok DNS

### Step 1 - Install & Configure DNS Server

**Tasks:**

> Configure jukung.lks.id and komodo.lks.id as DNS server.
>
> - Both needs to have identical record.
> - Both are the authoritative server of the lombok.id domain and lks.id domain.
> - Create both server record in the lombok.id domain as jukung.lombok.id and komodo.lombok.id that points to the Lombok Network IP address.
> 
> Create records of all servers in lks.id domain according to their hostname.
> - The record should points to all available IP Addresses in each server.

#### Installing DNS Server

On both server, open **Server Manager** and go to add new **Roles & Features**

```kotlin
Installation Type > Role based
Server Selection > <jukung/komodo>
Server Roles > DNS Server
Features > Skip
DNS Server > Next
Confirmation > Install
```

> Here I'll use `jukung.lks.id` as primary dns server and `komodo.lks.id` as secondary dns server

Open **Server Manager** in `jukung.lks.id` and select **Tools** section then click on **DNS**

After DNS Manager opened, select Server. There's should be appearing sub folders named `Forward Lookup Zones` and `Reverse Lookup Zones` . Right-Click on **Forward Lookup Zones** and select **New Zone**

![image-20231012101119443](https://github.com/diotriandika/learn-networking/assets/109568349/c63c3d36-1246-4396-92a6-d0b60a44f9f5)


```kotlin
// New Zone Wizard for lks.id
Next >
Primary Zone >
Zone Name (enter the zone name lks.id) >
Next >
Next >
Finish
// New Zone Wizard for lombok.id
Next >
Primary Zone >
Zone Name (enter the zone name lombok.id) >
Next >
Next >
Finish
```

Next,  create new Zone in`Reverse Lookup Zones` 

```kotlin
// New Zone Wizard 
Next >
Primary Zone >
IPv4 Reverse Lookup Zone >
Network ID (enter the network id, (10.196.10, 45.8.17., 10.200.2)) >
Next >
Next >
Finish
```

Add **DNS Record** according to the tasks

Reference :

- [Installing DNS](https://computingforgeeks.com/install-and-configure-dns-server-in-windows-server/)
- [DNS Forward](https://computingforgeeks.com/how-to-add-dns-forward-lookup-zone-in-windows-server/?expand_article=1)
- [DNS Reverse](https://computingforgeeks.com/how-to-add-dns-reverse-lookup-zone-in-windows-server/?expand_article=1)

#### Enable Primary Zone to allow Zone Transfering

Open **Server Manager** in `jukung.lks.id` and select **Tools** section then click on **DNS**

Select **zone**, then right-click and click on **properties** section. Look for **Zone Transfers** tab and tick the `Only to the following servers` section. Select **Edit** button then add Secondary DNS Server IP (komodo.lks.id ipv4 address). Click **Ok** and now select the **Notify** button below the **Edit** button  then add Secondary DNS Server IP (komodo.lks.id ipv4 address). 

![image-20231012104412300](https://github.com/diotriandika/learn-networking/assets/109568349/330c05cc-5481-408a-b4da-f8f6048fd0ee)


#### Configure Slave/Secondary DNS Server

Next, Open **Server Manager** in `komodo.lks.id` and select **Tools** section then click on **DNS**

After DNS Manager opened, select Server. There's should be appearing sub folders named `Forward Lookup Zones` and `Reverse Lookup Zones` . Right-Click on **Forward Lookup Zones** and select **New Zone**

![image-20231012101119443](https://github.com/diotriandika/learn-networking/assets/109568349/e8f6cce0-a045-458f-9dc9-68b016d00813)


```kotlin
// New Zone Wizard for lks.id
Next >
Secondary Zone >
Zone Name (enter the zone name lks.id) >
Add Master DNS Server (Enter jukung.lks.id ipv4 address) >
Next >
Finish
// New Zone Wizard for lombok.id
Next >
Secondary Zone >
Zone Name (enter the zone name lombok.id) >
Add Master DNS Server (Enter jukung.lks.id ipv4 address) >
Next >
Finish
```

Next,  create new Zone in `Reverse Lookup Zones` 

```kotlin
// New Zone Wizard 
Next >
Primary Zone >
IPv4 Reverse Lookup Zone >
Network ID (enter the network id, (10.196.10, 45.8.17., 10.200.2)) >
Add Master DNS Server (Enter jukung.lks.id ipv4 address) >
Next >
Finish
```

Refresh to reload the changes
