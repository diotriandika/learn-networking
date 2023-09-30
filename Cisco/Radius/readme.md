# Apa itu Radius?

Radius (Remote Authentication Dial-In User Service) adalah protokol yang digunakan untuk mengautentikasi (Authentication), mengotorisasi (Authorization) dan menghitung akuntasi (Accounting) untuk akses sebuah jaringan. Radius server memberikan skuritas tambahan pada jaringan, karena user yang ingin mengakses resource pada network, user tersebut harus melakukan otentikasi pada Radius Server. Dalam autentikasinya, Radius Server medukung PPP, PAP atau CHAP, Unix login, dan mekanisme otentikasi lainnya. 

Berikut rangkaian kinerja singkat mengenai Radius Server.

![image-20230930074604869](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20230930074604869.png)

> 1. User menginitiasi PPP otentikasi ke NAS (Network Access Server)
> 2. NAS menginformasikan user untuk menggunakan Username dan Password (jika menggunakan PAP) atau Challenge (jika menggunakan CHAP)
> 3. User menjawab
> 4. Radius Client mengirimkan username dan password yang sudah dienkripsi ke Radius Server
> 5. Radius Server merespon dengan Accept (Terima), Reject (Tolak), atau Challenge.
> 6. Radius Client sebagai service parameter yang menjadi satu dengan Accept atau Reject.

Dalam implemetasi Cisco, biasanya Radius client bekerja pada Cisco Router dan mengirim autentikasi request ke Radius Server pusat yang memiliki semua user autentikasi dan informasi akses network.

Referensi :

- https://www.cisco.com/c/en/us/td/docs/ios/12_2/security/command/reference/srfrad.html#:~:text=RADIUS%20is%20a%20distributed%20client,and%20network%20service%20access%20information.
- https://www.cisco.com/c/en/us/support/docs/security-vpn/remote-authentication-dial-user-service-radius/12433-32.html
- https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/sec_usr_rad/configuration/xe-16/sec-usr-rad-xe-16-book/sec-cfg-radius.html
- ChatGPT :)
