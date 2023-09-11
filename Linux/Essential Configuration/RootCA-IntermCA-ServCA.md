# Langkah - Langkah membuat & Self Sign CA
![image](https://github.com/diotriandika/learn-networking/assets/109568349/1f3c3532-7423-4aa6-981e-8def833d9b61)

> Quick Review :
> - **Root Certificate** adalah Certificate utama yang digunakan untuk mengeluarkan certificate digital ke server maupun client. Jika Root CA di trust oleh client/user, maka certificate yang disign oleh Root CA ini juga akan secara otomatis di trust oleh client.
> - **Intermediate CA** adalah sertificate subordinat (sub-certificate) yang dikeluarkan oleh otoritas sertifikat Root utuk tujuan penerbitan sertifikat untuk server atau client. Ini dilakukan dengan dnegan alasan keamanan, karena membuat sertifikat secara langsung dari sertifikat Root CA meningkatkan risiko kompromi sertifikat Root, dan jika sertifikat Root CA terganggu, seluruh infrastruktur kepercayaan yang dibangun oleh penyedia SSL akan gagal. Penggunaan serifikat perantara (Intermediate Certificate) untuk menerbitkan sertifikat SSL ke entitas akhir, karenanya akan memberikan tingkat keamanan tambahan.
> - **Server Certificate** adalah sertifikat yang digunakan utnuk mengautentikasi identitas server kepada client utnuk membanung koeksi yang aman dan terenkripsi

- Dalam pengerjaan disini, saya akan menggunakan contoh dari soal LKS ITNSA 2022
## Membuat Root CA
Pertama kita buat struktur direktori untuk penyimpanan CA nantinya

Masuk ke user ROOT
```
mkdir /root/ca
cd /root/ca

