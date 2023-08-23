# Objective :
![image](https://github.com/diotriandika/learn-networking/assets/109568349/23f839a3-4899-42b0-8379-4906e736fa23)
## Penyelesaian Task 1:
1. Extact file sample1.log yang terdapat "INFO"
   - `grep 'INFO' /var/log/sample1.log`
2. Copy output dari grep
   - CTRL + SHIFT + 6 atau bisa select langsung
   - `sudo echo 'enter_output_here' > ~/challenge01/output1.log`
3. Koreksi file menggunakan text editor
   - `sudo nano ~/challenge01/output1.log`
   - hapus line berlebih diakhir kemudian save
## Penyelesaian Task 2:
1. Extact file /var/log/sampel2.log yang terdapat "illegal" dan hanya line pertama.
   - `grep 'illegal' /var/log/sample2.log`
2. Copy output dari grep
   - CTRL + SHIFT + 6 atau bisa select langsung **Line pertama saja**
   - `sudo echo 'enter_output_here' > ~/challenge01/output2.log`
## Penyelesaian Task 3: 
1. Extact file /var/log/sample1.log yang terdapat `router_forward_getOI` dengan `TRACE` level
   -  `grep 'router_forward_getOI' /var/log/sample1.log`
2. Copy output dari grep
   - CTRL + SHIFT + 6 atau bisa select langsung
   - `sudo echo 'enter_output_here' > ~/challenge01/output3.log`
3. Koreksi file menggunakan text editor\
   - `nano ~/challenge01/output3.log`
   - hapus line yang tidak terdapat TRACE level, disini terindikasi dengan INFO
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/8492ec85-1d43-4dbd-822e-66db5cfde8c7)

![image](https://github.com/diotriandika/learn-networking/assets/109568349/5639e1ec-cd15-4b88-b7da-814e0b400ad8)
# **_You Completed The Task!_**
