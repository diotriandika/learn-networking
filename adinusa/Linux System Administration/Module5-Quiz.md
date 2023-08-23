# Objective
![image](https://github.com/diotriandika/learn-networking/assets/109568349/d1ecab93-984d-48bb-a9de-0ba1a4a9734d)
# Penyelesaian
1. Installasi Setup MariaDB
   - Download script mariadb `wget https://downloads.mariadb.com/MariaDB/mariadb_repo_setup`
   - jalankan setup menggunakan `	
curl -LsS https://r.mariadb.com/downloads/mariadb_repo_setup | sudo bash -s -- --mariadb-server-version="mariadb-10.9"`
   - berikan permmission execution scriptnya `chmod +x mariadb_repo_setup`
2. Specific Version MariaDB Installation
   - jalankan script `./mariadb_repo_setup --mariadb-server-version="mariadb-10.5.4"`
   - install mariadb `sudo apt-get install mariadb-sever mariadb-common`
   - verifikasi versi mariadb
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/8889e57a-d53b-4c40-be64-f6c0f29877fc)
source : https://www.dbi-services.com/blog/how-to-install-a-specific-version-of-mariadb/

![image](https://github.com/diotriandika/learn-networking/assets/109568349/1f57ce1d-8d3f-48d2-a577-c8382fc884fc)

#**_You Completed The Task!_**
