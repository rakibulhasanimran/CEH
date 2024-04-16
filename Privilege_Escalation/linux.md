https://github.com/SaiSathvik1/Linux-Privilege-Escalation-Notes

https://gtfobins.github.io/ is a valuable source that provides information on how any program, on which you may have sudo rights, can be used.



--------------
### SUID
- Find SUID or GUID bits set: ```find / -type f -perm -04000 -ls 2>/dev/null```


--------------
### Capabilities

- View Capabilities: ```getcap -r / 2>/dev/null```


--------------
