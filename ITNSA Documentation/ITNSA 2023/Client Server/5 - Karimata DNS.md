# Karimata DNS

Tasks:

---

- Configure rote.lks.id to server DNS for karimata.id

- Configure to forward all requests to (sub)domains other than karimata.id to Lombok DNS.

- Create records needed by the Karimata Web Service and Karimata Shared Folder

---

### Step 1 - Install DNS Server

Open **Server Manager** and go to add new **Roles & Features**

```kotlin
Installation Type > Role based
Server Selection > <rote>
Server Roles > DNS Server
Features > Skip
DNS Server > Next
Confirmation > Install
```

### Step 2 - Configure DNS Server

Open **Server Manager** and select **Tools** section then click on **DNS**

After DNS Manager opened, select Server. There's should be appearing sub folders named `Forward Lookup Zones` and `Reverse Lookup Zones` . Right-Click on **Forward Lookup Zones** and select **New Zone**

![image-20231012101119443](https://github.com/diotriandika/learn-networking/assets/109568349/c63c3d36-1246-4396-92a6-d0b60a44f9f5)


```kotlin
// New Zone Wizard for karimata.id
Next >
Primary Zone >
Zone Name (enter the zone name karimata.id) >
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
Network ID (enter the network id, (10.200.2)) >
Next >
Next >
Finish
```

Add **DNS Record** according to the tasks

www.karimata.id

> dalam soal tidak diberikan informasi terkait lombok.id


Reference :

- [Installing DNS](https://computingforgeeks.com/install-and-configure-dns-server-in-windows-server/)
- [DNS Forward](https://computingforgeeks.com/how-to-add-dns-forward-lookup-zone-in-windows-server/?expand_article=1)
- [DNS Reverse](https://computingforgeeks.com/how-to-add-dns-reverse-lookup-zone-in-windows-server/?expand_article=1)

### Configure Conditional Forwarders

Buka **DNS Manager** dan klik kanan pada **Conditional Forwarder** kemudian pilih **New Conditional Forwarder**

![image-20231012191827806](https://github.com/diotriandika/learn-networking/assets/109568349/0ec32b0f-7e83-42cf-a28a-d0eafce4b39f)


Tambahkan subdomain yang ingin diforward. lalu OK

> - lks.id
>
> - lombok.id
> - malaka.id

![image-20231012192655045](https://github.com/diotriandika/learn-networking/assets/109568349/e4fdb2ae-8c7a-4efb-a421-1085f870c064)


