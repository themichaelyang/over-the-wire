# OverTheWire: Bandit

## 0

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
cat readme
```

`bandit1: ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If`

## 1

```bash
ssh bandit1@bandit.labs.overthewire.org -p 2220
cat ./-
```

`bandit2: 263JGJPfgU6LtdEvgfWU1XP5yac29mFx`

## 2

```bash
ssh bandit2@bandit.labs.overthewire.org -p 2220
cat ./'--spaces in this filename--'
```

`bandit3: MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx`

## 3

```bash
ssh bandit3@bandit.labs.overthewire.org -p 2220
cd inhere
ls -A
cat ...Hiding-From-You
```

`bandit4: 2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ`

## 4

`find .` gets a list of all files in the directory, separated by newlines. This is piped into `xargs`, which splits by newlines and calls the given command with each line as the argument.

`file` gives the file type. 

Could go further and `| awk -F ': ' '{print $1}' | xargs cat`. `awk -F ': '` splits by `: ` and then returns the first item.

```bash
ssh bandit4@bandit.labs.overthewire.org -p 2220
cd inhere
find . | xargs file | grep ASCII
cat ./-file07
```

`bandit5: 4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw`

## 5

`find -depth -path './**/*'` recursively looks through files. 

`find -exec <command>` is similar to xargs, but doesn't require any escaping.

We use `du --bytes` and look for 1033 bytes (there are a lot of human readable files). 

To print the contents, we need `xargs`. Pipes will send the data to `stdin` not the `argv`, and `xargs` converts stdin into args.

```bash
ssh bandit5@bandit.labs.overthewire.org -p 2220
cd inhere
find -depth -path './**/*' -exec du --bytes '{}' ';' | grep 1033 | awk '{ print $2 }' | xargs cat
```

`bandit6: HWasnPhtq9AVKe0dmk45nxy20cvUa6EG`

## 6

Here, we filter by group and user, sending any stderr to `/dev/null` since there is quite a bit of "permission denied".

There's actually only 1 file with 33 bytes, so `du` the rest could have been omitted.

I think `find -depth -path './**/*` wasn't necessary in part 5, since `find /` worked just fine to print all files recursively in `/`.

```bash
find / -group bandit6 -user bandit7 -exec du --bytes '{}' ';' 2>/dev/null | grep 33 | awk '{print $2}' | xargs cat
```

`bandit7: morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj`

## 7

Can also use `vi`.

```bash
grep millionth data.txt | awk '{print $2}'
```

`bandit8: dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc`

## 8

```bash
sort data.txt | uniq --unique
```

`bandit9: 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM`

## 9

```bash
strings data.txt | grep '==='
```

`bandit10: FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey`

## 10

```bash
base64 --decode data.txt
```

`bandit11: dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr`

## 11

```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
```

`bandit12: 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4`

## 12

For this, I repeatedly piped the output to `| file -`. `-` indicates to use stdin as a file. This way I could tell what kind of archive format it was.

I didn't save it to a file because `gzip` refused to unzip files without the `.gz` suffix.

`tar -O -f -`: `-f -` means to use the file `-`, which is stdin, and `-O` means to pipe to stdout.

`gunzip` is the same as `gzip -d`, I think.

Eventually, I got ASCII, so I removed `| file -` and got the password.

```bash
xxd -r data.txt | gunzip | bzip2 -d | gunzip | tar --extract -O -f - | tar --extract -O -f - | bzip2 -d | tar --extract -O -f - | gunzip
```

`bandit13: FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`