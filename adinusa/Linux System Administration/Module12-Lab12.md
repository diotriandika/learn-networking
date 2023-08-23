# Objective
![image](https://github.com/diotriandika/learn-networking/assets/109568349/0fa756b7-4d23-4fe6-ba36-fc24802628b9)
# Penyelesaian Task 1
1. Generate key menggunakan `ssh-keygen`
# Penyelesaian Task 2
1. Copy public key ke btastudent@lab2.btech.id dengan port 2279
   - `ssh-copy-id -i .ssh/id_rsa.pub -p 2279 btastudent@lab2.btech.id`
# Penyelesaian Task 3
1. Login SSH dan View Authorized_Key
   - Login ke lab2.btech.id dengan mode non interactive `ssh -t -t btastudent@lab2.btech.id -p 2279`
   - source : https://stackoverflow.com/questions/17089808/how-to-do-remote-ssh-non-interactively
   - selanjutnya view menggunakan `cat .ssh/authorized_keys`
# Penyelesaian Task 4
1. View Hostname di lab2.btech.id
   - view hostname menggunakan command `hostname`
   - jika sudah selesai exit dari remote ssh

![image](https://github.com/diotriandika/learn-networking/assets/109568349/e0612bd6-019e-4540-be2f-8b72e0dca9c9)

# **_You Completed The Task!_**
