# Company VPN (Remote Access VPN)

**Tasks:**

---

- Configure Remote-Access VPN in buton.lks.id
  - Use openvpn
  - Allow clients to connect via Malaka Network only.
  - Only users in VPN OU are able to use the VPN
  - Distribute client configuration file to connect to the VPN to cilik.lks.id
    - Also install openvpn client in cilik.lks.id
    - Put the file in /etc/openvpn/client.ovpn
    - You can test the connection, but don't forget to disconnect again
  - Make sure client have access to both Karimata Network and Lombok Network after VPN is established.

---

### Step 1 - Install & Configure Certificate

Install openvpn in buton.lks.id and cilik.lks.id

```bash
$ sudo apt-get install openvpn
```

Create Self-signed certificate in buton.lks.id

```bash
# Copy easy-rsa 
$ sudo cp -r /usr/share/easy-rsa /etc/openvpn
# move to directory
$ cd /etc/openvpn/easy-rsa
```

Configure `vars`

```bash
# Create vars from template
$ sudo mv vars.example vars
# Edit fil
$ sudo nano -l vars
===
50 set_var EASYRSA "$PWD"
72 set_var EASYRSA_PKI
87 set_var EASYRSA_DN
95 - 100 sesuaikan
108 set_var EASYRSA_KEY_SIZE
117 set_Var EASYRSA_ALGO
125 set_var	EASYRSA_CA_EXPIRE
129 set_var EASYRSA_CERT_EXPIRE
158 set_var EASYRSA_NS_COMMENT "Easy-RSA Generated Certificate"
190 set_var EASYRSA_EXT_DIR
202 set_var EASYRSA_SSL_CONF
214 set_var EASYRSA_DIGEST
---
```

Create public key

```bash
$ ./easyrsa init-pki
```

Create root ca

```bash
$ ./easyrsa build-ca nopass
===
buton.lks.id
enter
===
```

Generate VPN Server & Client Certificate and Key

```bash
$ ./easyrsa gen-req vpnserver nopass
===
enter
===
$ ./easyrsa gen-req vpnclient nopass
===
enter
===
```

Sign Certiifcate

```bash
$ ./easyrsa sign-req server vpnserver
===
yes
===
$ ./easyrsa sign-req client vpnclient
===
yes
===
```

Generate Diffie-Hellman key for exchange

```bash
$ ./easyrsa gen-dh
```

Move Required ca

```bash
# Moving required vpnserver certificate
$ sudo cp /etc/openvpn/easy-rsa/pki/ca.crt /etc/openvpn/server
$ sudo cp /etc/openvpn/easy-rsa/pki/dh.pem /etc/openvpn/server
$ sudo cp /etc/openvpn/easy-rsa/pki/issued/vpnserver.crt /etc/openvpn/server
$ sudo cp /etc/openvpn/easy-rsa/pki/private/vpnserver.key /etc/openvpn/server

# Moving required vpnclient certificate
$ sudo cp /etc/openvpn/easy-rsa/pki/ca.crt /etc/openvpn/client
$ sudo cp /etc/openvpn/easy-rsa/pki/issued/vpnclient.crt /etc/openvpn/client
$ sudo cp /etc/openvpn/easy-rsa/pki/private/vpnclient.key /etc/openvpn/client
```

### Step 2 - Configure VPN Server

Create new file named `server.conf` in `/etc/openvpn/` and add the configuration line

```bash
$ sudo nano server.conf
===
port 1194
proto udp
dev tun
ca /etc/openvpn/server/ca.crt
cert /etc/openvpn/server/vpnserver.crt
key /etc/openpvn/server/vpnserver.key
dh /etc/openvpn/server/dh.pem
server 10.250.2.0 255.255.255.0
push "redirect-gateway def1"
keepalive 10 300
comp-lzo
user nobody
group nogroup
persist-tun
persist-key
log-append /var/log/server.log
verb 3
===
```

Start server configuration and enable for auto load while booting

```bash
$ sudo systemctl start openvpn@server
$ sudo systemctl enable openvpn@server
```

### Step 3 - Configure VPN Client

> do in buton.lks.id

move to directory `/etc/openvpn/client`, then create configuration file named `client.ovpn`

```bash
$ sudo nano client.ovpn
===
client
proto udp
dev tun
remote 45.8.17.115 1194
comp-lzo
user nobody
group nogroup
persist-tun
persist-key
verb 3
===
```

Import CA

```bash
# Importing CA
$ sudo cat /etc/openvpn/client/ca.crt >> /etc/openvpn/client/client.ovpn
```

Next add new parameter for embedding ceritficates into openvpn config

```bash
$ sudo nano /etc/openvpn/client/client.ovpn
===
...
persist-tun
persist-key
verb 3
<ca>	# <--- start embedding ca
---BEGIN CERTIFICATE---
XXXX
XXXX
---END CERTIFICATE---
</ca>	# <--- end embedding ca
===
```

Now do the same thing to import Certificate and Private Key

```bash
# Embedding Certificate
$ sudo cat /etc/openvpn/client/vpnclient.crt >> /etc/openvpn/client/client.ovpn
$ sudo nano /etc/openvpn/client/client.ovpn
===
...
persist-tun
persist-key
verb 3
<ca>	
---BEGIN CERTIFICATE---
XXXX
XXXX
---END CERTIFICATE---
</ca>
<cert>	# <-- start embedding certificate
---BEGIN CERTIFICATE---
XXXX
XXXX
---END CERTIFICATE---
</cert> # <-- end embedding certificate
===
# Embedding Private Key
$ sudo cat /etc/openvpn/client/vpnclient.key >> /etc/openvpn/client/client.ovpn
$ sudo nano /etc/openpvn/client/client.ovpn
===
...
persist-tun
persist-key
verb 3
<ca>	
---BEGIN CERTIFICATE---
XXXX
XXXX
---END CERTIFICATE---
</ca>
<cert>	
---BEGIN CERTIFICATE---
XXXX
XXXX
---END CERTIFICATE---
</cert> 
<key>	# <-- start embedding private key
---BEGIN PRIVATE KEY---
XXXX
XXXX
---END PRIVATE KEY---
</key>	# <--- end embedding private key
===
```

Copy `client.ovpn` to cilik.lks.id home directory

```bash
$ scp /etc/openvpn/client/client.ovpn user@45.8.17.31:/home/user
```

Open cilik.lks.id server, then move the file to `/etc/openvpn`

```bash 
$ sudo mv /home/user/client.ovpn /etc/openvpn
```

### Verify

In cilik.lks.id run the `client.ovpn` vpn config

```bash
$ sudo openvpn --config /etc/openvpn/client.ovpn &
```

> `&` parameter here for be able the user to detach / background running the session while running config
>
> If log appearing, enter `CTRL+C`

Next check the interface & routing information

```bash
$ ip a
$ ifconfig
$ ip route
```

Then try to pinging Lombok Network & Karimata Network

```bash
$ ping 10.196.10.xx
$ ping 10.200.2.xx
```

Done

**Referensi:**

- https://www.rosehosting.com/blog/how-to-set-up-an-openvpn-server-on-debian-10/
- https://www.howtoforge.com/how-to-install-and-configure-openvpn-server-on-debian-10/
- https://stackoverflow.com/questions/70945854/run-openvpn-client-in-background
- https://askubuntu.com/questions/298419/how-to-disconnect-from-openvpn
- https://www.brainfart.sg/index.php/2012/05/embedding-certificate-into-openvpn-config/



