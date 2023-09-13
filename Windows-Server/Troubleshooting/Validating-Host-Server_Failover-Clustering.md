### Problem : 
![image](https://github.com/diotriandika/learn-networking/assets/109568349/7aa68880-fbda-4350-8ad3-40c0b17928dd)
### Solver :
Run in both server on powershell admin
```powershell
New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```
