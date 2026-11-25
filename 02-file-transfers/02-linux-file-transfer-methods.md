## Linux File Transfer Methods

1. download `flag.txt` using python directly from the web root.

```
python3 -c "import urllib.request; urllib.request.urlretrieve('http://$target_ip/flag.txt', 'flag.txt')"
```

`-c` - run code from terminal.

`urllib.request` - python's module for accessing urls and downloading their contents.

`;` - separator

`urlretrieve(URL, filename)` - download dir and save file.

2. upload the file into the target machine's ssh server using scp.

```
scp ./82b4928b-6bfa-4da5-9377-5263107d6866.zip htb-student@$target_ip:/home/htb-student/
```

`/home/htb-student` - the home directory (~).

output:
```
htb-student@10.129.234.168's password: 
82b4928b-6bfa-4da5-9377-5263107d6866.zip                 100%  194     3.6KB/s   00:00
```

3. connect with ssh into the target machine.

```
ssh htb-student@$target_ip
```

output:
```
htb-student@nix04:~$
```

4. list files and directories.

```
htb-student@nix04:~$ ls
```

output:
```
82b4928b-6bfa-4da5-9377-5263107d6866.zip
```

5. unzip the file using python.

```
htb-student@nix04:~$ python3 -m zipfile -e 82b4928b-6bfa-4da5-9377-5263107d6866.zip .
htb-student@nix04:~$ ls

```

`-m zipfile` - use the zipfile module.

`-e` - specify which zip file to extract.

`.` - present working directory.

output:
```
82b4928b-6bfa-4da5-9377-5263107d6866.zip  upload_nix.txt
```

6. run hasher to `upload_nix.txt`.

```
htb-student@nix04:~$ hasher upload_nix.txt
```