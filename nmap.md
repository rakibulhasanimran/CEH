Firewall Bypass ```sudo nmap -sN [Machine_IP]```


### Live Hostes
```nmap -sn -PR 192.168.18.0-255```

### FQDN
```sudo nmap --script smb-os-discovery.nse <IP>```


### Scripts
```cd /usr/share/nmap/scripts; ls | grep <script_name>```
