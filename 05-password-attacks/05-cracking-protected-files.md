## Cracking Protected Files

1. unzip the provided zip file.

```
unzip c2d77580-c371-45c4-9af4-e964d8bdeef4.zip
```

output:
```
Archive:  c2d77580-c371-45c4-9af4-e964d8bdeef4.zip
  inflating: Confidential.xlsx
```

2. find available jtr extractors.

```
find /usr/share/john -iname '*2john.py' 2>/dev/null
```

`-iname` - case insensitive.

`'*2john.py'` - prefix wildcard ending in '2john.py'.

`2>/dev/null` - disregard error messages.

3. extract the password hash using office2john.

```
/usr/share/john/office2john.py Confidential.xlsx > Confidential-xlsx.hash
```

4. read hash to identify office version.

```
cat Confidential-xlsx.hash
```

output:
```
Confidential.xlsx:$office$*2013*100000*256*16*cb0e251cdec92e97eeb38e595cd4eb09*58758c88f3bb25e43e1e21adbd4b6e50*0057c1ae71b0023424ba705607dc0df1d9a786974bb957a821cfd7e39129eb15
```

its office 2013.

5. list hashcat office modes.

```
hashcat -hh | grep -i office
```

output:
```
   9400 | MS Office 2007                                             | Document
   9500 | MS Office 2010                                             | Document
   9600 | MS Office 2013                                             | Document
  25300 | MS Office 2016 - SheetProtection                           | Document
   9700 | MS Office <= 2003 $0/$1, MD5 + RC4                         | Document
   9710 | MS Office <= 2003 $0/$1, MD5 + RC4, collider #1            | Document
   9720 | MS Office <= 2003 $0/$1, MD5 + RC4, collider #2            | Document
   9810 | MS Office <= 2003 $3, SHA1 + RC4, collider #1              | Document
   9820 | MS Office <= 2003 $3, SHA1 + RC4, collider #2              | Document
   9800 | MS Office <= 2003 $3/$4, SHA1 + RC4                        | Document
```

6. double check current hash file.

```
cat Confidential-xlsx.hash
```

output:
```
Confidential.xlsx:$office$*2013*100000*256*16*cb0e251cdec92e97eeb38e595cd4eb09*58758c88f3bb25e43e1e21adbd4b6e50*0057c1ae71b0023424ba705607dc0df1d9a786974bb957a821cfd7e39129eb15
```

7. remove "Confidential.xlsx:".

```
echo '$office$*2013*100000*256*16*cb0e251cdec92e97eeb38e595cd4eb09*58758c88f3bb25e43e1e21adbd4b6e50*0057c1ae71b0023424ba705607dc0df1d9a786974bb957a821cfd7e39129eb15' > Confidential-xlsx.hash
```

8. use hashcat mode `9600` to crack the xlsx office 2013 password hash.

```
hashcat -m 9600 -a 0 Confidential-xlsx.hash /usr/share/wordlists/rockyou.txt
```