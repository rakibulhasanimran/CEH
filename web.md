### Wappalyzer

Wappalyzer (https://www.wappalyzer.com/) is an online tool and browser extension that helps identify what technologies a website uses, such as frameworks, Content Management Systems (CMS), payment processors and much more, and it can even find version numbers as well.


### Automation Tools

Although there are many different content discovery tools available, all with their features and flaws, we're going to cover three which are preinstalled on our attack box, ffuf, dirb and gobuster.


Using ffuf:
```console
ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -u http://10.10.199.0/FUZZ
```


Using dirb:
```console
dirb http://10.10.199.0/ /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
```

Using Gobuster:
```console
gobuster dir --url http://10.10.199.0/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
```

## Subdomain Enumeration
### SSL/TLS Certificates
When an SSL/TLS (Secure Sockets Layer/Transport Layer Security) certificate is created for a domain by a CA (Certificate Authority), CA's take part in what's called "Certificate Transparency (CT) logs". These are publicly accessible logs of every SSL/TLS certificate created for a domain name. The purpose of Certificate Transparency logs is to stop malicious and accidentally made certificates from being used. We can use this service to our advantage to discover subdomains belonging to a domain, sites like https://crt.sh and https://ui.ctsearch.entrust.com/ui/ctsearchui offer a searchable database of certificates that shows current and historical results.


### Bruteforce DNS
Bruteforce DNS (Domain Name System) enumeration is the method of trying tens, hundreds, thousands or even millions of different possible subdomains from a pre-defined list of commonly used subdomains. Because this method requires many requests, we automate it with tools to make the process quicker. In this instance, we are using a tool called dnsrecon to perform this.
Tools: [dnsrecon](https://www.kali.org/tools/dnsrecon/)
```console
dnsrecon -t brt -d acmeitsupport.thm
```

### OSINT subdomain discovery
To speed up the process of OSINT subdomain discovery, we can automate the above methods with the help of tools like [Sublist3r](https://github.com/aboul3la/Sublist3r)

```console
./sublist3r.py -d acmeitsupport.thm
```

### Virtual Hosts
Some subdomains aren't always hosted in publically accessible DNS results, such as development versions of a web application or administration portals. Instead, the DNS record could be kept on a private DNS server or recorded on the developer's machines in their /etc/hosts file (or c:\windows\system32\drivers\etc\hosts file for Windows users) which maps domain names to IP addresses. 

Because web servers can host multiple websites from one server when a website is requested from a client, the server knows which website the client wants from the Host header. We can utilise this host header by making changes to it and monitoring the response to see if we've discovered a new website.

Like with DNS Bruteforce, we can automate this process by using a wordlist of commonly used subdomains.

```console
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://10.10.137.225
```

The above command uses the `-w` switch to specify the wordlist we are going to use. The `-H` switch adds/edits a header (in this instance, the Host header), we have the `FUZZ` keyword in the space where a subdomain would normally go, and this is where we will try all the options from the wordlist.

Because the above command will always produce a valid result, we need to filter the output. We can do this by using the page size result with the `-f`s switch. Edit the below command replacing `{size}` with the most occurring size value from the previous result and try it on the AttackBox.

```console
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://10.10.137.225 -fs {size}
```

This command has a similar syntax to the first apart from the `-fs` switch, which tells ffuf to ignore any results that are of the specified size.
