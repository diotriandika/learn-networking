# Apa itu Ansible?

Ansible adalah sebuah tools untuk automasi IT. Ansible dapat mengkonfigurasi system, deploy software, and orchestrate task IT yang lebih advance seperti layaknya continuous deployments.

> Continuous Deployment (CD) atau Continuous Delivery (CD) adalah **praktek pengiriman perangkat lunak ke lingkungan produksi secara otomatis setelah melalui tahap integrasi, pengujian, dan verifikasi**

Tujuan utama dari Ansible adalah Simplicity dan Ease-of-Use. Ansible juga fokus dalam sekuritas dan realibilitas, menggunakan OpenSSH sebagai teransport yang memberikan sekuritas lebih ketika melakukan remote login.

## Getting Started with Ansible

Ansible mengotomasikan managemen dari remote system dan sekaligus mengontrol sesuai dengan status yang diperlukan. Dalam environment Ansible terdapat tiga komponen utama, yakni

- Control Node

  Sebuah system dimana Ansible terinstal. Sebagai contoh kita bisa menjalankan command ansible seperti `ansible` ,`ansible-playbooks` atau `ansible-inventory` didalam Control Node.

- Managed Node

  Sebuah remote system atau host yang dikontrol oleh Ansible.

- Inventory

  Sebuah list yang berisi Managed Node yang secara logis terorganize. Kita bisa membuat inventory pada Control Node untuk mendefinisikan host deployments kepada Ansible.

Ansible bekerja dengan menghubungkan ke nodes (client, servers atau apapun itu yang ingin dikonfigurasi) dalam sebuah network, lalu mengirimkan sebuah program yang bernama Ansible Module ke node tersebut. Ansible mengekesusi module tersebut melalui SSH dan menghilangkan module tersebut jika selesai. Dalam melakukan hal ini, Ansible Control Node hanya memerlukan akses login ke Managed Node untuk melakukan semua task yang diperlukan. 

