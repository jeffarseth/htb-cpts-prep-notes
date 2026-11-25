## Writing Custom Wordlists and Rules

1. install bopscrk.

```
sudo apt update && sudo apt upgrade -y

sudo apt install bopscrk
```

2. uncomment `#Config.setup()`.

```
sudo nano /usr/lib/python3/dist-packages/bopscrk/modules/__init__.py
```

3. disable `recursive_leet`.

```
sudo nano /usr/lib/python3/dist-packages/bopscrk/bopscrk.cfg
```

4. generate the wordlist.

```
bopscrk -w '05081998,08051998,19980508,19980805,august5,august51998,alex,baseball,bella,maria,nexura,nexuraltd,nexuralimited,sanfrancisco,california,bellamariaalex,sanfranciscocausa' -m 12 -M 24 -c -l -o custom.wl
```

`-w` - keywords. separated by commas.

`-m` - minimum length in characters.

`-M` - maximum length in characters.

`-c` - enable capitalization transformations.

`-l` - enable leet transformations.

`-o` - output file name.

5. crack the hash using the wordlist made with bopscrk.

```
hashcat -a 0 -m 0 97268a8ae45ac7d15c3cea4ce6ea550b custom.wl
```

that didn't work, try another method.

6. make an initial wordlist with mark's information.

```
echo "mark
white
08051998
1998
august
nexura
nexuraltd
nexuralimited
sanfrancisco             
california
usa
sf
ca
bella
maria
alex
baseball" > init-custom.wl
```

7. make a custom rule file.

```
echo ':           
c
so0
c so0
sa@
c sa@
c sa@ so0
$!
$! c  
$! so0
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa' > custom.rule
```

8. combine words in pairs.

```
hashcat --stdout -a 1 init-custom.wl init-custom.wl > custom-pairs.wl
```

`--stdout` - generate candidates without cracking hashes.

`-a 1` -  hashcat uses the combinator attack.

9. filter minimum length to 12.

```
awk 'length($0) >= 12' custom-pairs.wl > custom-filtered.wl
```

`awk` - processes each line.

`length($0)` - measures the current line's character count.

`>= 12` - keeps only entries at least 12 characters long.

10. apply `custom.rule`.

```
hashcat --stdout -r custom.rule custom-filtered.wl > custom-final.wl
```

`-r` - rule file

11. crack the hash with the final wordlist.

```
hashcat -a 0 -m 0 97268a8ae45ac7d15c3cea4ce6ea550b custom-final.wl
```