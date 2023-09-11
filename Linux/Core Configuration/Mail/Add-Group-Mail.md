# Cara menambahkan user ke mailgroup
1. edit file `/etc/aliases`
   ```
   ## Tambahkan Line Dibawah
   <group-name>: <user>@<domain>, <user>@<domain>, dst
   ```
2. Apply dengan command
   ```
   sudo postalias /etc/aliases
   ```
3. Restart service postfix
