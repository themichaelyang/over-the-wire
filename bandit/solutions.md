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
ssh bandit12@bandit.labs.overthewire.org -p 2220
xxd -r data.txt | gunzip | bzip2 -d | gunzip | tar --extract -O -f - | tar --extract -O -f - | bzip2 -d | tar --extract -O -f - | gunzip
```

`bandit13: FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`

## 13

```bash
ssh bandit13@bandit.labs.overthewire.org -p 2220
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
cat /etc/bandit_pass/bandit14
```

`bandit14: MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS`

## 14

`<<<` is a here-string, which converts the arg into stdin (without an echo and pipe)

```bash
ssh bandit14@bandit.labs.overthewire.org -p 2220
nc localhost 30000 <<< MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
```

`bandit15: 8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`

## 15

`-quiet` to suppress the handshake output. For some reason, piping won't work without it, but you can interactively paste the password.

ChatGPT says `-ign_eof` also works to allow piping, because `-quiet` is also changing `s_client` to not quit on EOF, which is included at the end of herestrings.

```bash
openssl s_client -crlf -connect localhost:30001 -quiet <<< 8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

`bandit16: kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`

## 16

```
ssh bandit16@bandit.labs.overthewire.org -p 2220
nmap localhost -p 31000-32000 | grep -E '[0-9]+/tcp' | awk -F '/' '{print $1}'
openssl s_client -crlf -connect localhost:31790 -quiet <<< kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```

```
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY----
```

Paste into temp file, SSH using the temp file.
Saving it locally didn't work (thanks Florian).

```
mktemp | xargs vi
```

## 17

Remember to SSH into bandit17 from bandit16.

```
ssh -i /tmp/tmp.wPX8cjv1kA bandit17@bandit.labs.overthewire.org -p 2220
diff passwords.old passwords.new
ssh bandit18@bandit.labs.overthewire.org -p 2220
```

`bandit18: x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`

## 18

SSH allows passing of passwords directly into it, so no shell is needed.

```
ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"
```

`bandit 19: cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8`

## 19

```
./bandit20-do cat /etc/bandit_pass/bandit20
```

`bandit20: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO`

## 20

```
ssh bandit20@bandit.labs.overthewire.org -p 2220
screen
```

In the `screen`, listen on port 2000:

```
nc -l 2000
```

Exit screen with `CTRL+A`, `D`.

Start the command as a background job with `&`.

```
./suconnect 2000 &
```

Reconnect to `screen` with the running `nc` and enter the password.

```
screen -r
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

`bandit21: EeoULMCra2q0dSkYj561DX7s1CpBuOBt`

## 21

In this challenge, we find out that a cron is copying the bandit22 password into a temp file every minute.

```
cd /etc/cron.d
vi cronjob_bandit22
vi /usr/bin/cronjob_bandit22.sh
cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

`bandit22: tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q`

## 22

Similar to 21, a cron is running every minute copying the password to a temp file for bandit23, but the temp file is obscured.

```
cd /etc/cron.d
vi cronjob_bandit23
vi /usr/bin/cronjob_bandit23.sh
echo I am user bandit23 | md5sum | cut -d ' ' -f 1
cat /tmp/8ca319486bfbbc3663ea0fbe81326349
```

`bandit23: 0Zf11ioIjMVN551jX3CmStKLYqjk54Ga`

## 23

```
cd /etc/cron.d
vi cronjob_bandit24
vi /usr/bin/cronjob_bandit24.sh
```

In `/usr/bin/cronjob_bandit24.sh`:

```
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname/foo
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```

It is executing all scripts in `/var/spool/bandit24` owned by `bandit23`.

```
cd /var/spool/
ls -la
```

We can see this directory has a subdirectory `foo` which we can write to (but not read from).

It may be helpful to work in a temp directory so we can copy the script since it gets deleted every cron:

```
cd $(mktemp -d)
```

