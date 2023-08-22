# __Objective__
![image](https://github.com/diotriandika/learn-networking/assets/109568349/06697487-3e3f-4983-9e5f-75d42347d3ca)
# Penyelesaian Task 1
1. List file dulu
2. Find file yang dipastikan SUID & SGID bit set.
   - `find '/directory_location' -type f \( -perm -4000 -o -perm -2000 \) -exec ls -l {} \;`
   - source : https://www.thegeekdiary.com/linux-unix-how-to-find-files-which-has-suid-sgid-set/
3. copy output dan masukan di ~/challenge02/suid.txt
   - `sudo echo 'insert_output-here' > '/directory_tujuan'`
4. edit file suid.txt kemudian rapihkan line dibelakang seperti dibawah.
   - `nano ~/challenge02/suid.txt`
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/325f9dfe-c1f3-4e52-aeea-c2e415a1f57d)
# Penyelesaian Task 2
1. check file dengan MODE set permissionnya dengan `-perm`
   - `find '/directory_tujuan' -perm 'MODE'`
   - source : https://ostechnix.com/find-files-based-permissions/
   - > dalam kasus ini `find ~/challenge02/specials/ -perm 600`
2. copy semua file example*.txt sesuai output yang muncul
   - `sudo cp ~/challenge02/specials/example*txt ~/challenge02/backup`
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/991e9dab-f6d6-4b34-b32b-8e4eb40f69b1)
# Penyelesaian Task 3
1. list direktori filenya dulu
   - `ln -l ~/challenge02/spesials/` atau `ln -lh ~/challenge02/spesials/`
   - cek size setiap file, disini terlihat bahwa minimum size itu `8192/8K` dan maximum size filenya di `12288/12K`, tapi yang dibutuhin disini cuma range 8K sampai 10K
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/d72c3c9e-82dd-4520-8dc8-9a65dbfd7538)
2. find file sesuai dengan range size task
   - `find ~/challenge2/spesials/ -size +8193 -size -10241`
   - > kenapa engga 8192/10240? kyknya size filenya lebih dari itu, jadi gpp kalo dilebihin lagi 1 byte dari value yang udah dilist tadi
   - source : https://unix.stackexchange.com/questions/569322/find-files-recursively-of-size-range
3. copy file example*.txt sesuai dengan output yang keluar
   - `sudo cp ~/challenge02/spesials/example*.txt ~/challenge02/size/`
# Penyelesaian Task 4
1. list direktori filenya dulu
   - `ln -l ~/challenge02/spesials/`
   - cek user apa saja yang ada, kasus disini cuma ada 2 user yaitu root dan student.
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/d72c3c9e-82dd-4520-8dc8-9a65dbfd7538)
2. find file sesuai owner selain student, karena objektifnya disini ngelist file yang tidak dimiliki oleh student. karena disini cuma ada user root dan student jadi list user root aja.
   - `find ~/challenge02/specials -user root`
   - source : https://www.cyberciti.biz/faq/how-do-i-find-all-the-files-owned-by-a-particular-user-or-group/
   - masukan output ke ~/challenge02/extension.txt menggunakan echo
   - `sudo echo 'enter_output_here" > ~/challenge02/extension.txt`
   - pastikan file sudah sesuai, bila perlu koreksi line lebih menggunakan text editor
![image](https://github.com/diotriandika/learn-networking/assets/109568349/81e36d7a-b5d5-4d94-9ed4-a310328e4251)

# __*You Accomplished The Task!*__
