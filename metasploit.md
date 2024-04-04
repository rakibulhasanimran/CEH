You can search exploits using the `search` command, obtain more information about the exploit using the `info` command, and launch the exploit using `exploit`. While the process itself is simple, remember that a successful outcome depends on a thorough understanding of services running on the target system.

Most of the exploits will have a preset default payload. However, you can always use the `show payloads` command to list other commands you can use with that specific exploit.

Once you have decided on the payload, you can use the `set payload` command to make your choice.

Some payloads will open new parameters that you may need to set, running the `show options` command once more can show these.



### meterpreter
Search File:
`search -f <filename>`

Help:
`help`



# Post-Exploitation
Commands mentioned previously, such as `getsystem` and `hashdump` will provide important leverage and information for privilege escalation and lateral movement. Meterpreter is also a good base you can use to run post-exploitation modules available on the Metasploit framework. Finally, you can also use the load command to leverage additional tools such as Kiwi or even the whole Python language.
