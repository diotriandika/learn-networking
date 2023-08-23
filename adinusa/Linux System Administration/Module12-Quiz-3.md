# Objective
![image](https://github.com/diotriandika/learn-networking/assets/109568349/03d2a56e-503f-4aec-823e-ffa0c4d0ae0e)
# Penyelesaian Task 
1.  User Configuration
   - Buat user menggunakan `useradd`
   - change default password user menggunakan `passwd`
   - change default shell menggunakan command `usermod --shell <shell> <user>`
   - verify menggunakan `cat /etc/passwd`
   - source : https://www.geeksforgeeks.org/how-to-change-default-shell-in-linux/
2. Modify User
   - gunakan `passwd --expire <user>` agar user bisa mengganti password ketika login pertama kali.
3. Managing Group
   - buat group `mentor` menggunakan `groupadd <group-name>`
   - tambahkan user `lnearher` ke group `mentor` dengan command `usermod -G <group> <user>`

![image](https://github.com/diotriandika/learn-networking/assets/109568349/a8be9425-45c9-4c95-a0dc-8d432c520beb)
# **_You Completed The Task!_**
