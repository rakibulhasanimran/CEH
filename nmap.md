Firewall Bypass ```sudo nmap -sN [Machine_IP]```


### Live Hostes
```nmap -sn -PR 192.168.18.0-255```

### FQDN
```sudo nmap --script smb-os-discovery.nse <IP>```


### Scripts
```cd /usr/share/nmap/scripts; ls | grep <script_name>```


---------------------
# Firewall and IDS/IPS Evasion

- ```sudo nmap -sN [Machine_IP]```


### OS name
- ```nmap -T4 -A -v 10.129.2.80 -D RND:5 --stats-every=5s``` https://forum.hackthebox.com/t/answer-of-firewall-and-ids-ips-evasion-easy-lab/282532
- ```nmap -sV -T2 --top-ports 100 [Target IP]``` https://handsonkb.blog/2023/11/18/htb-network-enumeration-with-nmap/


### DNS service version
- ```sudo nmap -sSU -p53 –script dns-nsid [Target IP]```
