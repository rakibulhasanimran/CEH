### Wappalyzer

Wappalyzer (https://www.wappalyzer.com/) is an online tool and browser extension that helps identify what technologies a website uses, such as frameworks, Content Management Systems (CMS), payment processors and much more, and it can even find version numbers as well.


### Automation Tools

Although there are many different content discovery tools available, all with their features and flaws, we're going to cover three which are preinstalled on our attack box, ffuf, dirb and gobuster.


Using ffuf:
```console
user@machine$ ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -u http://10.10.199.0/FUZZ
```


Using dirb:
```console
user@machine$ dirb http://10.10.199.0/ /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
```

Using Gobuster:
```console
user@machine$ gobuster dir --url http://10.10.199.0/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
```
