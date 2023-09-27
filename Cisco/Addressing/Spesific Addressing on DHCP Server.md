```markdown
Router(config)# ip dhcp pool <pool-name>
Router(dhcp-config)# host <static-ip> <subnetmask>
Router(dhcp-config)# client-identifier <mac-address-client>
Router(dhcp-config)# client-name <hostname>
Router(dhcp-config)# default-router <default-gateway>
Router(dhcp-config)# dns-server <dns-server> (optional)
Router(dhcp-config)# exit

```
Reference by:
- https://www.oreilly.com/library/view/cisco-ios-cookbook/0596527225/ch20s08.html
- https://networklessons.com/cisco/ccie-routing-switching/dhcp-static-binding-on-cisco-ios
