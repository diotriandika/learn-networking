```powershell
for ($i = 1; $i -le 10;$i++) {
	if ($i -eq 10) {
	Add-DnsServerResourceRecordA -Name "OPS$i" -ZoneName "itnsa.id" -IPv4Address "172.16.34.11"
	Add-DnsServerResourceRecordA -Name "OPS$i" -ZoneName "itnsa.id" -IPv4Address "172.16.34.12"
	} else {
  	Add-DnsServerResourceRecordA -Name "OPS0$i" -ZoneName "itnsa.id" -IPv4Address "172.16.34.11"
	Add-DnsServerResourceRecordA -Name "OPS0$i" -ZoneName "itnsa.id" -IPv4Address "172.16.34.12"
	}
}
```
