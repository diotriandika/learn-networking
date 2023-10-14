```kotlin
sudo iptables -A FORWARD -i ens36 -o ens33 -s 10.196.10.0/25 -d 45.8.17.0/24 -j DROP
```
