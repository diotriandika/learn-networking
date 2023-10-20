Jika terjadi error pada L3SW di interface yang tergubung ke Router EST, rubah duplex pada interface di Router EST yang mengarah pada Switch
```kotlin
interface gigabitEthernet 0/0
duplex full
exit
interface gigabitEthernet 0/1
duplex full
exit
```
