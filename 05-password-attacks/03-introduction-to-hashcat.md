## Introduction to Hashcat

1. use a dictionary attack to crack the first password hash.

```
hashcat -a 0 -m 0 e3e3ec5831ad5e7288241960e5d4fdb8 /usr/share/wordlists/rockyou.txt
```

`-a 0` - attack mode 0. straight/dictionary attack

`-m 0` - hash mode 0. md5.

2. use a dictionary attack with rules to crack the second password hash.

```
hashcat -a 0 -m 0 1b0556a75770563578569ae21392630c /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule
```

`-r` - rule file. 66 common rules. list rules with:
```
dpkg -L hashcat-data | grep -E '\.rule$'
```

3. use a mask attack to crack the third password hash.

```
hashcat -a 3 -m 0 1e293d6912d074c0fd15844d803400dd '?u?l?l?l?l?d?s'
```

`-a 3` - mask attack (brute force).

`?u` - one uppercase letter.

`?l` - one lowercase letter.

`?d` - one digit (0-9).

`?s` - one special character.