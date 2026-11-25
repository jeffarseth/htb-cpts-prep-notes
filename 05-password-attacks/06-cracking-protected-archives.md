## Cracking Protected Archives

1. download the `download` zip file.

```
curl -O "http://${target_ip}:${target_port}/download"
```

2. unzip `download`.

```
unzip download
```

output:
```
Archive:  download
  inflating: Private.vhd
```

3. find available jtr extractors.

```
find /usr/share/john -iname '*2john.py' 2>/dev/null
```

`-iname` - case insensitive.

`'*2john.py'` - prefix wildcard ending in '2john.py'.

`2>/dev/null` - disregard error messages.

4. extract the password hash using bitlocker2john.

```
/usr/share/john/bitlocker2john.py Private.vhd > Private-vhd.hash
```

output:
```
[!] Supplied image path is not a BitLocker partition. Try specifiying the offset of the BitLocker partition with -o
```

the script cannot find a bitLocker partition at the beginning of `Private.vhd`.

5. find the bitlocker partition by inspecting the partition table.

```
mmls Private.vhd
```

output:
```
DOS Partition Table
Offset Sector: 0
Units are in 512-byte sectors

      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001   Primary Table (#0)
001:  -------   0000000000   0000000127   0000000128   Unallocated
002:  000:000   0000000128   0000125055   0000124928   NTFS / exFAT (0x07)
003:  -------   0000125056   0000131071   0000006016   Unallocated
```

byte offset = starting sector * sector size

6. calculate the byte offset.

byte offset = 128 * 512 = 65536

7. run bitlocker2john with the offset.

```
/usr/share/john/bitlocker2john.py Private.vhd -o 65536 > Private-vhd.hash
```

8. read bitlocker hash data.

```
cat Private-vhd.hash
```

output:
```
$bitlocker$0$16$b3c105c7ab7faaf544e84d712810da65$1048576$12$b020fe18bbb1db0103000000$60$e9c6b548788aeff190e517b0d85ada5daad7a0a3f40c4467307011ac17f79f8c99768419903025fd7072ee78b15a729afcf54b8c2e3af05bb18d4ba0
$bitlocker$1$16$b3c105c7ab7faaf544e84d712810da65$1048576$12$b020fe18bbb1db0103000000$60$e9c6b548788aeff190e517b0d85ada5daad7a0a3f40c4467307011ac17f79f8c99768419903025fd7072ee78b15a729afcf54b8c2e3af05bb18d4ba0
$bitlocker$2$16$1d06e8b3aa5f14ddcb5ad7415c1d24f5$1048576$12$b020fe18bbb1db0106000000$60$8ff2e9cdd6ec25df9060e95e3c490a9aefd5601f4cefa47da810339f809a90187cbbd907bd1077565293727114f3c218c078e03f2f8baa214f1de800
$bitlocker$3$16$1d06e8b3aa5f14ddcb5ad7415c1d24f5$1048576$12$b020fe18bbb1db0106000000$60$8ff2e9cdd6ec25df9060e95e3c490a9aefd5601f4cefa47da810339f809a90187cbbd907bd1077565293727114f3c218c078e03f2f8baa214f1de800
$bitlocker$0$16$b3c105c7ab7faaf544e84d712810da65$1048576$12$b020fe18bbb1db0103000000$60$e9c6b548788aeff190e517b0d85ada5daad7a0a3f40c4467307011ac17f79f8c99768419903025fd7072ee78b15a729afcf54b8c2e3af05bb18d4ba0
$bitlocker$1$16$b3c105c7ab7faaf544e84d712810da65$1048576$12$b020fe18bbb1db0103000000$60$e9c6b548788aeff190e517b0d85ada5daad7a0a3f40c4467307011ac17f79f8c99768419903025fd7072ee78b15a729afcf54b8c2e3af05bb18d4ba0
$bitlocker$2$16$1d06e8b3aa5f14ddcb5ad7415c1d24f5$1048576$12$b020fe18bbb1db0106000000$60$8ff2e9cdd6ec25df9060e95e3c490a9aefd5601f4cefa47da810339f809a90187cbbd907bd1077565293727114f3c218c078e03f2f8baa214f1de800
$bitlocker$3$16$1d06e8b3aa5f14ddcb5ad7415c1d24f5$1048576$12$b020fe18bbb1db0106000000$60$8ff2e9cdd6ec25df9060e95e3c490a9aefd5601f4cefa47da810339f809a90187cbbd907bd1077565293727114f3c218c078e03f2f8baa214f1de800
```

9. remove duplicates.

```
sort -u Private-vhd.hash > Private-vhd2.hash

cat Private-vhd2.hash
```

output:
```
$bitlocker$0$16$b3c105c7ab7faaf544e84d712810da65$1048576$12$b020fe18bbb1db0103000000$60$e9c6b548788aeff190e517b0d85ada5daad7a0a3f40c4467307011ac17f79f8c99768419903025fd7072ee78b15a729afcf54b8c2e3af05bb18d4ba0
$bitlocker$1$16$b3c105c7ab7faaf544e84d712810da65$1048576$12$b020fe18bbb1db0103000000$60$e9c6b548788aeff190e517b0d85ada5daad7a0a3f40c4467307011ac17f79f8c99768419903025fd7072ee78b15a729afcf54b8c2e3af05bb18d4ba0
$bitlocker$2$16$1d06e8b3aa5f14ddcb5ad7415c1d24f5$1048576$12$b020fe18bbb1db0106000000$60$8ff2e9cdd6ec25df9060e95e3c490a9aefd5601f4cefa47da810339f809a90187cbbd907bd1077565293727114f3c218c078e03f2f8baa214f1de800
$bitlocker$3$16$1d06e8b3aa5f14ddcb5ad7415c1d24f5$1048576$12$b020fe18bbb1db0106000000$60$8ff2e9cdd6ec25df9060e95e3c490a9aefd5601f4cefa47da810339f809a90187cbbd907bd1077565293727114f3c218c078e03f2f8baa214f1de800
```

`$bitlocker$0$` - user password, fast attack mode.
`$bitlocker$1$` - user password, with mac verification.
`$bitlocker$2$` - recovery password, fast attack mode.
`$bitlocker$3$` - recovery password, with mac verification.

10. identify hashcat mode for bitlocker.

```
hashcat -hh | grep -i bitlocker
```

output:
```
  22100 | BitLocker                                                  | Full-Disk Encryption (FDE)
```

11. crack the first hash.

```
hashcat -a 0 -m 22100 '$bitlocker$0$16$b3c105c7ab7faaf544e84d712810da65$1048576$12$b020fe18bbb1db0103000000$60$e9c6b548788aeff190e517b0d85ada5daad7a0a3f40c4467307011ac17f79f8c99768419903025fd7072ee78b15a729afcf54b8c2e3af05bb18d4ba0' /usr/share/wordlists/rockyou.txt
```

12. install dislocker to decrypt and mount bitlocker-encrypted windows drives.

```
sudo apt install dislocker
```

13. create mount points for the `dislocker-file` and the decrypted filesystem.

```
sudo mkdir -p /mnt/dislocker /mnt/bitlocker
```

`-p` - create any parent directories that don't already exist (`/mnt` in this example).

14. unlock the bitlocker volume.

```
sudo dislocker -V Private.vhd -O 65536 -u -- /mnt/dislocker
```

`-V` - the virtual disk image.

`-O` - the bitlocker partition byte offset.

`-u` - use user password authentication.

`--` - end dislocker options

15. mount the decrypted filesystem.

```
sudo mount -o loop /mnt/dislocker/dislocker-file /mnt/bitlocker
```

`-o loop` - treat the input file as a filesystem-backed block device.

16. browse its contents.

```
cd /mnt/bitlocker

ls
```

output:
```
flag.txt  'System Volume Information'
```

17. read the flag.

```
cat flag.txt
```

18. when finished, unmount `/mnt/bitlocker` first then `/mnt/dislocker` to safely disconnect the drive.

```
sudo umount /mnt/bitlocker

sudo umount /mnt/dislocker
```