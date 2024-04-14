**1.** In the first two cases, we checked the code for the web app, and then we knew how to exploit it. However, in this case, we are performing black box testing, in which we don't have the source code. In this case, errors are significant in understanding how the data is passed and processed into the web app.

In this scenario, we have the following entry point: http://webapp.thm/index.php?lang=EN. If we enter an invalid input, such as ```THM```, we get the following error

```diff
- Warning: include(languages/THM.php): failed to open stream: No such file or directory in /var/www/html/THM-4/index.php on line 12
```


The error message discloses significant information. By entering ```THM``` as input, an error message shows what the include function looks like:  ```include(languages/THM.php);```. 

If you look at the directory closely, we can tell the function includes files in the languages directory is adding  ```.php``` at the end of the entry. Thus the valid input will be something as follows:  ```index.php?lang=EN```, where the file EN is located inside the given languages directory and named  ```EN.php```. 

Also, the error message disclosed another important piece of information about the full web application directory path which is ```/var/www/html/THM-4/```

To exploit this, we need to use the ```../``` trick, as described in the directory traversal section, to get out the current folder. Let's try the following:

```http://webapp.thm/index.php?lang=../../../../etc/passwd```

Note that we used 4 ```../``` because we know the path has four levels ```/var/www/html/THM-4```. But we still receive the following error:

```diff
- Warning: include(languages/../../../../../etc/passwd.php): failed to open stream: No such file or directory in /var/www/html/THM-4/index.php on line 12
```
It seems we could move out of the PHP directory but still, the include function reads the input with .php at the end! This tells us that the developer specifies the file type to pass to the include function. To bypass this scenario, we can use the NULL BYTE, which is ```%00```.

Using null bytes is an injection technique where URL-encoded representation such as ```%00``` or ```0x00``` in hex with user-supplied data to terminate strings. You could think of it as trying to trick the web app into disregarding whatever comes after the Null Byte.

By adding the Null Byte at the end of the payload, we tell the  include function to ignore anything after the null byte which may look like:

```include("languages/../../../../../etc/passwd%00").".php");``` which equivalent to → ```include("languages/../../../../../etc/passwd");```


NOTE: the ```%00``` trick is fixed and not working with PHP 5.3.4 and above.


Now apply what we showed in Lab #3, and try to read files /etc/passwd, answer question #1 below.



2. In this section, the developer decided to filter keywords to avoid disclosing sensitive information! The /etc/passwd file is being filtered. There are two possible methods to bypass the filter. First, by using the NullByte %00 or the current directory trick at the end of the filtered keyword /.. The exploit will be similar to http://webapp.thm/index.php?lang=/etc/passwd/. We could also use http://webapp.thm/index.php?lang=/etc/passwd%00.

To make it clearer, if we try this concept in the file system using cd .., it will get you back one step; however, if you do cd ., It stays in the current directory.  Similarly, if we try  /etc/passwd/.., it results to be  /etc/ and that's because we moved one to the root.  Now if we try  /etc/passwd/., the result will be  /etc/passwd since dot refers to the current directory.

Now apply this technique in Lab #4 and figure out to read /etc/passwd.



3. Next, in the following scenarios, the developer starts to use input validation by filtering some keywords. Let's test out and check the error message!

http://webapp.thm/index.php?lang=../../../../etc/passwd

We got the following error!

Warning: include(languages/etc/passwd): failed to open stream: No such file or directory in /var/www/html/THM-5/index.php on line 15


If we check the warning message in the include(languages/etc/passwd) section, we know that the web application replaces the ../ with the empty string. There are a couple of techniques we can use to bypass this.

First, we can send the following payload to bypass it: ....//....//....//....//....//etc/passwd

Why did this work?

This works because the PHP filter only matches and replaces the first subset string ../ it finds and doesn't do another pass, leaving what is pictured below.



Try out Lab #5 and try to read /etc/passwd and bypass the filter!



4. Finally, we'll discuss the case where the developer forces the include to read from a defined directory! For example, if the web application asks to supply input that has to include a directory such as: http://webapp.thm/index.php?lang=languages/EN.php then, to exploit this, we need to include the directory in the payload like so: ?lang=languages/../../../../../etc/passwd.
