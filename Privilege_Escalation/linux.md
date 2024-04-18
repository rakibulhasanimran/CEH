https://github.com/SaiSathvik1/Linux-Privilege-Escalation-Notes

https://gtfobins.github.io/ is a valuable source that provides information on how any program, on which you may have sudo rights, can be used.

--------------
### First try
 ```sudo -i```


--------------
### Sudo
Check: ```sudo -l```


--------------
### SUID
- Find SUID or GUID bits set: ```find / -type f -perm -04000 -ls 2>/dev/null```


--------------
### Capabilities

- View Capabilities: ```getcap -r / 2>/dev/null```


--------------

### Cron Jobs
- Check: ```cat /etc/crontab```

--------------
### Path
- See Path: ```echo $PATH```
- Add Path at First: ```export PATH=/tmp:$PATH```

### NFS
NFS (Network File Sharing) configuration is kept in the ```/etc/exports``` file. This file is created during the NFS server installation and can usually be read by users.

To check: ```cat /etc/exports```

The critical element for this privilege escalation vector is the “no_root_squash” option you can see above. By default, NFS will change the root user to nfsnobody and strip any file from operating with root privileges. If the “no_root_squash” option is present on a writable share, we can create an executable with SUID bit set and run it on the target system.

We will start by enumerating mountable shares from our attacking machine.

- ```showmount -e 10.10.143.128```

- 


