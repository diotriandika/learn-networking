# Company Mail

**Tasks:**

---

- Configure buton.lks.id as the central mail server
  - Use any application that support both SMTP and IMAP using negotiable TLS
  - Use the domain lks.id, so email can be sent to user@lks.id email address.
  - Enable SMTP wiht negotiable TLS on port 25
  - Enable IMAP with negotiable TLS on port 143
  - Use certificates from Karimata Root Certificate
- Enable web-based using roundcube
  - Make it accessible using domain mail.lks.id
  - Enable HTTPS access using certificate from Karimata Root Certificate
  - Do not respond to HTTP requests
- Make sure the SMTP and IMAP only respond to request from Karimata Network
- Create two mail user: admin@lks.id and user@lks.id with password Skills39
- Create email alias contact@lks.id should be received by admin@lks.id

---

## Install & Configure Mail Server

### Step 1 - Installing & Configuring SMTP & IMAP

Install postfix & Dovecot

```bash
$ sudo apt-get install postfix dovecot-imapd
---
Internet Site
lks.id
---
```

Edit konfigurasi postfix main.cf

```bash
$ sudo nano /etc/postfix/main.cf
---
46 inet_interfaces = 10.200.2.0/25

48 home_mailbox = Maildir/
---
```

Buat direktori mail

```bash
$ sudo maildirmake.dovecot /etc/skel/Maildir
```

Reconfigure Postfix

```bash
$ sudo dpkg-reconfigure postfix
---
Internet Site
lks.id
ok
ok
no
0.0.0.0/0 #tambahakan diakhir
ok
ok
ipv4
---
```

Edit Konfigurasi dovecot

```bash
# Set Listen
$ sudo nano -l /etc/dovecot/dovecot.conf
---
30 listen = *

48 login_trusted_networks = 10.200.2.0/25
---
# Move to conf.d directory
$ cd /etc/dovecot/conf.d/
# Edit 10-mail.conf
$ sudo nano 10-mail.conf
---
24 mail_location = maildir:~/Maildir # uncomment
30 # add comment
---
```

Tambahakan user dan alias

```bash
# Add user
$ sudo adduser admin
$ sudo adduser user
# Add Alias
$ sudo nano /etc/aliases
---
contact: admin
---
$ sudo newaliases
```

Restart Service

```bash
$ sudo systemctl restart postfix dovecot
```

### Step 2 - Installing & Configuring Webmail

Install mariadb & roundcube

```bash
$ sudo apt-get install mariadb-server rounducube
---
yes
Skills39
Skills39
---
```

Edit file konfigurasi roundcube

```bash
$ sudo nano /etc/roundcube/config.inc.php
---
36 $config['default_host'] = 'mail.lks.id';

48 $config['smtp_server'] = 'lks.id';

55 $config['smtp_port'] = '51';

55 $config['smtp_user'] = '';

49 $config['smtp_pass'] = '';
---
```

Reconfigure Roundcube

```bash
$ sudo dpkg-reconfigure roundcube-core
---
ok
ok
no
check only apache2
restart yes
keep current version
---
```

Edit file konfigurasi apache

```bash
# include roundcube configuration
$ sudo nano /etc/apache/apache2.conf
---
# add the end of line
Include /etc/roundcube/apache.conf
---
# edit site configuration
$ sudo nano /etc/apache2/sites-available/000-default.conf
---
ServerName mail.lks.id
ServerAdmin admin@lks.id
DocumentRoot /usr/share/roundcube
---
```

Restart semua service

```bash
$ sudo systemctl restart postfix dovecot apache2
```

### Step 3 - Create Certificate for TLS/SSL

Pindah ke direktori `/home/user` kemudian Request certificate

```bash
$ sudo openssl req -new -nodes -out mail.lks.id.csr -newkey rsa:2048 -keyout mail.lks.id.key
```

Kirim file `mail.lks.id.csr` ke rote.lks.id untuk ditandatangani

> Pastikan ketika export menggunakan encoding DER

Jika sudah kirim kembali file `mail.lks.id.cer` ke buton.lks.id

Selanjutnya convert `mail.lks.id.cer` menjadi file `crt`

```bash
openssl x509 -inform DER -in mail.lks.id.cer -out mail.lks.id.crt
```

Certificate sekarang sudah bisa digunakan

### Step 4 - Enable TLS/SSL

Edit file konfigurasi postfix

```bash
# move to postfix directory
$ cd /etc/postfix
# edit main.cf
$ sudo nano -l main.cf
---
49 smtpd_tls_cert_file = /home/user/mail.lks.id.crt
50 smtpd_tls_key_file = /home/user/mail.lks.id.key
---
# edit master.cf
$ sudo nano -l master.cf
---
# uncomment line 17 - 20 & line 29 - 32
submission inet n       -       y       -       -       smtpd
  -o syslog_name=postfix/submission
  -o smtpd_tls_security_level=encrypt
  -o smtpd_sasl_auth_enable=yes
  
submission inet n       -       y       -       -       smtpd
  -o syslog_name=postfix/submission
  -o smtpd_tls_security_level=encrypt
  -o smtpd_sasl_auth_enable=yes
---
```

Edit file konfigurasi Dovecot

```bash
# move to dovecot conf.d directory
$ cd /etc/dovecot/conf.d
# edit 10-auth.conf
$ sudo nano -l 10-auth.conf
---
10 disable_plaintext_auth = yes

100 auth_mechanisms = plain login
---
# edit 10-master.conf
$ sudo nano -l 10-master.conf
---
# uncomment line 19
17 service imap-login {
18 	inet_listener imap {
19 		port = 143
20  }
# uncomment line 107 - 109
107 unix_listener /var/spool/postfix/private/auth {
108 	mode = 0666
109 }
---
# edit 10-ssl.conf
$ sudo nano -l 10-ssl.conf
---
12 ssl_cert = </home/user/mail.lks.id.crt
13 ssl_key = </home/user/mail.lks.id.key
```

Restart kedua service

```bash
$ sudo systemctl restart postfix dovecot
```

Edit file konfigurasi site apache2

```bash
$ sudo nano /etc/apache2/sites-available/000-default.conf
---
# ganti VirtualHost untuk listen ke 443
<VirtualHost *:443>
	ServerName mail.lks.id
	ServerAdmin admin@lks.id
	DocumentRoot /usr/share/roundcube
# Tambahkan certificate dan key
	SSLEngine on
	SSLCertificateFile /home/user/mail.lks.id.crt
	SSLCertificateKeyFile /home/user/mail.lks.id.key
</VirtualHost>
```

Restart Service

```bash
$ sudo systemctl restart apache2
```

### Verif

Coba saling send pesan, coba juga alias `contact` apa bisa direct ke `admin`

