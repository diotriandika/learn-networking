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

   
