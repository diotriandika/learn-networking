# Objective
![image](https://github.com/diotriandika/learn-networking/assets/109568349/254d5df4-d4b3-4d3d-bad3-2e300c8f8646)
# Penyelesaian Task 1
1. Cek dan mengganti file ownership  
   - cek informasi file dengan command `ls -l <file>`
   - ganti file ownership secara rekursif dengan cara `chown -R <username:group-name> <file>`
# Penyelesaian Task 2
1. Setting Group+S/Special Permission file
   - menambahkan SGID bit permission ke ~/quiz4/answer/perm dengan command `sudo chmod g+s <file-directory/location>`
   - source https://www.redhat.com/sysadmin/suid-sgid-sticky-bit
# Penyelesaian Task 3
1. Menghapus file sampah
   - `rm` file `garbage` dengan command `sudo rm ~/quiz4/answer/garbage`
# Pneyelesaian Task 4
1. Mengganti permmission file dengan `chmod`
   - cek dulu permission dari user, group dan other dari filenya dengan command `ls -l <file-directory/location>`
   - akan keluar output seperti dibawah
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/3f69eade-be9e-42c2-ae49-a1abbe97240e)
   - lalu sesuaikan dengan permission yang diberikan di task, dalam kasus ini kita rubah permission <user:read,write,execute> <group:read,execute> dan <other:none>
   - jalankan command `chmod` untuk modify permission
   - `chmod u+x,g+x,o-r ~/quiz4/answer/permissions.txt` & `chmod g-w ~/quiz4/answer/permissions.txt`
> short explaination. dalam syntax diatas yang dimaksud dari `u+x,g+x,o-r` itu adalah opsi modify.
> u=user, g=group, dan o=other.
> r=read, w=write, x=execute
> penggunaan +/- disini bermaksud apa kita mau menambahkan permission apa menghilangkan permission tersebut.
# Penyelesaian Task 5
1. Menambahkan ACL ke file dan memberikan permssion <user> sesuai task
   - Lihat status ACL dari file dengan command `getfacl <file-directory/location>`
   - set ACL user dengan `setfacl <-m/-x> u:<user>:<r/w/x> <file-directory/location>`
   - di task ini `setfacl -m u:lnearher:w ~/quiz4/answer/acl_mnop.txt`
   - source : https://www.geeksforgeeks.org/access-control-listsacl-linux/

![image](https://github.com/diotriandika/learn-networking/assets/109568349/d2a44a94-f637-4381-af67-330e0847dc9e)
# **_You Completed The Task!_**
