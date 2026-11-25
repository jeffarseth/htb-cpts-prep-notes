## Introduction to John the Ripper

1. make a `passwd` file for `r0lf`'s password hash.

```
echo 'r0lf:$6$ues25dIanlctrWxg$nZHVz2z4kCy1760Ee28M1xtHdGoy0C2cYzZ8l2sVa1kIa8K9gAcdBP.GI6ng/qA4oaMrgElZ1Cb9OeXO4Fvy3/:0:0:Rolf Sebastian:/home/r0lf:/bin/bash' > passwd
```

2. use single-crack mode to crack `r0lf`'s password.

```
john --single passwd
```

3. make `ripemd-128` file to store the RIPEMD-128 hash.

```
echo '193069ceb0461e1d40d216e32c79c704' > ripemd-128
```

4. locate the `rockyou.txt` wordlist.

```
find / -type f -name "rockyou.txt" 2>/dev/null
```

5. search for ripemd-128 format name in the jtr format list.

```
john --list=formats | grep -i ripemd
```

output:
```
439 formatsRaw-SHA256, Raw-SHA3, Raw-SHA384, restic, ripemd-128, ripemd-160, rsvp, 
 (151 dynamic formats shown as just "dynamic_n" here)
tcp-md5, telegram, tezos, Tiger, timeroast, tc_aes_xts, tc_ripemd160, 
tc_ripemd160boot, tc_sha512, tc_whirlpool, vdi, OpenVMS, vmx, VNC, vtp, wbb3,
```

6. crack `ripemd-128` using the `rockyou.txt` wordlist with the ripemd-128 format.

```
john --wordlist=/usr/share/wordlists/rockyou.txt --format=ripemd-128 ripemd-128
```