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

Could go further and `| awk -F ': ' '{print $1}' | xargs cat`. `awk` splits by `: ` and then returns the first item.

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

