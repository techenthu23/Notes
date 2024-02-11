
# What does “`sh a.sh <&0 >&0`” mean?

`n>&p` and `n<&p` are the same operator and are for duplicating the file descriptor (fd) `p` onto the file descriptor `n`. Or said otherwise, they redirect the file descriptor `n` to whatever resource fd `p` is redirected to.

The `<` and `>` are not used to determine what direction (reading or writing) the redirected file descriptor will be used. `n` will get the same direction as `p`. That is, if `p` was open for writing, so will be n even if the `n<&p` operator is used.

The only difference between the two operators is when `n` is not specified. `>&p` redirects stdout (is like `1>&p` or `1<&p`) and `<&p` redirects stdin (is like `0<&p` or `0>&p`).

So `<&0` is like `0<&0`, so redirects stdin to whatever resource stdin was redirected to, so does nothing useful, it's usually a no-op and doesn't make much sense.

But not always a no-op, not in every shell. When job control is disabled, POSIX requires stdin of command in the background to be redirected to `/dev/null` or an equivalent file. In Bash (tested in 4.4.12) `<&0` overrides this. Compare (`ls -l /proc/self/fd/0 &`) and (`<&0 ls -l /proc/self/fd/0 &`). In some cases this is useful.

`>&0` duplicates the `fd 0` onto the `fd 1`. Because the `fd 1` (stdout), is by convention only used for writing, that `>&0` only makes sense if `fd 0` was open in read+write mode.

That would be the case in cases where `fd 0` points to the terminal device, because terminal emulators or getty would generally open the terminal device in read+write mode and assign `fds 0, 1 and 2` to it.

So maybe whoever wrote that wanted to redirect stdout to the terminal assuming that stdin was pointing to it.

One place where n>&n makes sense is with zsh and its mult_IOs feature. In zsh:

```bash
some-cmd >&1 > some-file
# that is: some-cmd 1>&1 1> some-file
```

Redirects the standard output of some-cmd to both whatever stdout was before (&1) and some-file, as if you had written:

```bash
some-cmd | tee some-file
```

While

```bash
some-cmd <&0 < some-file
# that is: some-cmd 0<&0 0< some-file
```

would feed first the original stdin and then some-file as input to some-cmd as if you had written:

```bash
cat - some-file | some-cmd
```

But in cmd `<&0 >&0,` `fd 0` is redirected only once, so that does not apply.

`n>&n` can also have an interesting side effect in some shells (ksh, zsh, not dash, bash nor yash) in that it triggers an error and gives up running the command if the file descriptor n is not open. So, in those shells,

```bash
cmd 0<&0
```

would avoid running cmd in the pathological condition where stdin is closed:

```
$ ksh -c 'cat file - <&0' <&-
ksh: 0: cannot open [Bad file descriptor]
$ mksh -c 'cat file - <&0' <&-
mksh: <&0 : bad file descriptor
$ zsh -c 'cat file - <&0' <&-
zsh:1: 0: bad file descriptor

$ bash -c 'cat file - <&0' <&-
contents of file
cat: -: Bad file descriptor
cat: closing standard input: Bad file descriptor
```

In this case, `<&0` is redundant (this is the default behaviour). `>&0` will mean it prints what would otherwise be printed on standard out to standard in instead, relative to the parent shell. Most ttys will echo such output, and so in consequence the redirection has almost no effect and probably no visible effect.


```bash
toupper(){
	<&0 tr $lowercase_letters $uppercase_letters >&1
}

tolower(){
	<&0 tr $uppercase_letters $lowercase_letters >&1
}

lowercase_letters=abcdefghijklmnopqrstuvwxyz
uppercase_letters=ABCDEFGHIJKLMNOPQRSTUVWXYZ

trkey_lower_filename=`echo Test_filename | tolower`
```


# References:

- [Advanced Bash-Scripting Guide](https://tldp.org/LDP/abs/html/ "An in-depth exploration of the art of shell scripting")