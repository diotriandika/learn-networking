# Malaka Website 

**Tasks:**

---

- Configure aur.lks.id to server a bunch of user websites
  - You can use any service/application
- Add the homepage www.malaka.id with content specified in the appendix
  - Use HTTPS with certificate from Karimata Root Certificate
  - Server the page in both HTTP and HTTPS
  - Use /var/www/home to store all this website files
- Add 10 user websites:
  - user01.malaka.id stored at /var/www/user01
  - user02.malaka.id stored at /var/www/user02
  - user03.malaka.id stored at /var/www/user03
  -  …
  - user09.malaka.id stored at /var/www/user09
  - user10.malaka.id stored at /var/www/user10
- Enable basic authentication for all 10 user websites.
  - For user01.malaka.id, use username user01 and password Skills39
  - For user02.malaka.id, use username user02 and password Skills39
  - For user03.malaka.id, use username user03 and password Skills39
  - …
  - For user09.malaka.id, use username user09 and password Skills39
  - For user10.malaka.id, use username user10 and password Skills39

- All user website content is the same:

  ```html
  <html><h1>This is user website. The content is not yet changed.</h1></html>
  ```

- Add all required DNS Record in Malaka DNS

---

### Step 1 - Configure Malaka Homepage

Install nginx dan apache2-utils

```bash
$ sudo apt install nginx apache2-utils
```

> nginx sebagai penyedia webserver
>
> apache2-utils sebagai service untuk auth

Pindah ke workdir

```bash
$ cd /etc/nginx/sites-available
```

Edit file konfigurasi site

```bash
$ sudo nano default
---
# delete semua dan rubah menjadi seperti dibawah
server {
	listen 80;
	server_name www.malaka.id;
	location / {
		root /var/www/home;
		index index.html;
	}
}
server {
	listen 443 ssl;
	server_name www.malaka.id;
	ssl_certificate /home/user/malaka.id.crt;
	ssl_certificate_key /home/user/malaka.id.key;
	location / {
		root /var/www/home;
		index index.html;
	}
}
---
```

Buat direktori untuk index & edit

```bash
$ sudo mkdir /var/www/home
$ sudo nano /var/www/home/index.html
---
<html><head>
<title>Malaka Homepage</title>
</head><body>
<h1>PT Malaka</h1>
<p>Copyright 2023. Hak Cipta dilindungi oleh undang-undang.</p>
</body> </html>
---
```

Issue certificate ke Rote Root Certificate

```bash
$ cd /home/user
$ openssl req -new -nodes -out malaka.id.csr -newkey rsa:2048 -keyout malaka.id.key
```

Sign di Rote Root Certificate lalu kirim kembali  ke aur.lks.id

Convert certificate

```bash
$ openssl x509 -inform DER -in malaka.id.cer -out malaka.id.crt
```

Restart nginx

```bash
$ sudo systemctl restart nginx
```

### Step 2 - Configure User Website

Pindah ke workdir

```bash
$ cd /etc/nginx/sites-available
```

Buat template untuk site user

```bash
$ sudo nano template
---
server {
	listen 80;
	server_name username.malaka.id;
	location / {
		root /var/www/username;
		index index.html;
		auth_basic "Restricted";
		auth_basic_user_file /etc/nginx/.username;
	}
}
---
```

Selanjutnya buat script

```bash
$ sudo nano user-site.sh
```

Buat Looping

```sh
#! /bin/bash
for i in {1..10}
do
	if [ $i -eq 10 ];
	then
		
	elif [ $i -lt 10 ];
	then
	
	fi
done
```

Tambahkan line untuk mengcopy template site 

```sh
#! /bin/bash
for i in {1..10}
do
	if [ $i -eq 10 ];
	then
		cp template user$i
		sed -i 's/username/user'$i'/g' user$i
	elif [ $i -lt 10 ];
	then
	   	cp template user0$i
		sed -i 's/username/user0'$i'/g' user0$i
	fi
done
```

Tambahkan line untuk create direktori dan index user

```sh
#! /bin/bash
for i in {1..10}
do
	if [ $i -eq 10 ];
	then
		cp template user$i
		sed -i 's/username/user'$i'/g' user$i
		mkdir /var/www/user$i
		echo "<html><h1>This is user website. The content not yet changed</h1></html>" /var/www/user$i/index.html
	elif [ $i -lt 10 ];
	then
	   	cp template user0$i
		sed -i 's/username/user0'$i'/g' user0$i
		mkdir /var/www/user0$i
		echo "<html><h1>This is user website. The content not yet changed</h1></html>" > /var/www/user0$i/index.html
	fi
done
```

Tambahkan line untuk create user & password auth

```bash 
#! /bin/bash
for i in {1..10}
do
	if [ $i -eq 10 ];
	then
		cp template user$i
		sed -i 's/username/user'$i'/g' user$i
		mkdir /var/www/user$i
		echo "<html><h1>This is user website. The content not yet changed</h1></html>" /var/www/user$i/index.html
		htpasswd -c -b "/etc/nginx/.user$i" "user$i" "Skills39"
	elif [ $i -lt 10 ];
	then
	   	cp template user0$i
		sed -i 's/username/user0'$i'/g' user0$i
		mkdir /var/www/user0$i
		echo "<html><h1>This is user website. The content not yet changed</h1></html>" > /var/www/user0$i/index.html
		htpasswd -c -b "/etc/nginx/user0$i" "user0$i" "Skills39"
	fi
done
```

Tambahkan line untuk membuat symlink

```sh
#! /bin/bash
for i in {1..10}
do
	if [ $i -eq 10 ];
	then
		cp template user$i
		sed -i 's/username/user'$i'/g' user$i
		mkdir /var/www/user$i
		echo "<html><h1>This is user website. The content not yet changed</h1></html>" /var/www/user$i/index.html
		htpasswd -c -b "/etc/nginx/.user$i" "user$i" "Skills39"
		ln -s /etc/nginx/sites-available/user$i /etc/nginx/sites-enabled/
	elif [ $i -lt 10 ];
	then
	   	cp template user0$i
		sed -i 's/username/user0'$i'/g' user0$i
		mkdir /var/www/user0$i
		echo "<html><h1>This is user website. The content not yet changed</h1></html>" > /var/www/user0$i/index.html
		htpasswd -c -b "/etc/nginx/user0$i" "user0$i" "Skills39"
		ln -s /etc/nginx/sites-available/user0$i /etc/nginx/sites-enabled/
	fi
done
```



Buat file script agar bisa diexec

```bash
$ sudo chmod +x user-site.sh
```

Execute script

```bash
$ sudo ./user-site.sh
```

Restart Nginx

```bash
$ sudo systemctl restart nginx
```

### Step 3 - Add DNS Record

Buka server buton.lks.id

Pindah ke workdir

```bash
$ cd /etc/bind
```

Buat script untuk menambahkan entry DNS Record baru

```bash
$ sudo nano user-dns.sh
---
#! /bin/bash
for i in {1..10}
do
	if [ $i -eq 10 ];
	then
		echo "user$i	IN	A	45.8.17.23" >> forward #(File DNS Forward)
	elif [ $i -lt 10 ];
	then
		echo "user0$i	IN	A	45.8.17.23" >> forward #(File DNS Forward)
	fi
done
---
```

Buat file script agar bisa diexec

```bash
$ sudo chmod +x user-dns.sh
```

Execute script

```bash
$ sudo ./user-dns.sh
```

Cek DNS Record

Restart bind9

```bash
$ sudo systemctl restart bind9
```

### Testing

Coba buka di jukung.lks.id atau lainnya

Referensi :

- https://lordrukie.medium.com/vsc-itnsa-3-konfigurasi-multiple-subdomain-nginx-web-server-5e2abe76182c
- https://www.digitalocean.com/community/tutorials/how-to-set-up-password-authentication-with-nginx-on-ubuntu-14-04
- https://www.howtoforge.com/tutorial/basic-http-authentication-with-nginx/
- https://techexpert.tips/nginx/enable-https-nginx/

- https://unix.stackexchange.com/questions/141436/too-many-levels-of-symbolic-links
