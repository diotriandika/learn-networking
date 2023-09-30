```powershell
for ($i=1;$i -le 10;$i++) {
	if ($i -eq 10) {
		New-Item -Path "C:\web\ops$i\index.html" -ItemType File -Force
		echo "<h1> ops$i.itnsa.id </h1>" >> "C:\web\ops$i\index.html"
		echo "This website is managed by ops@itnsa.id" >> "C:\web\ops$i\index.html"
		New-Website -Name ops$i -Port 80 -HostHeader ops$i.itnsa.id -PhysicalPath "C:\web\ops$i"
	} else {
		New-Item -Path "C:\web\ops0$i\index.html" -ItemType File -Force
		echo "<h1> ops0$i.itnsa.id </h1>" >> "C:\web\ops0$i\index.html"
		echo "This website is managed by ops@itnsa.id" >> "C:\web\ops0$i\index.html"
		New-Website -Name ops0$i -Port 80 -HostHeader ops0$i.itnsa.id -PhysicalPath "C:\web\ops0$i"
	}
}
```
