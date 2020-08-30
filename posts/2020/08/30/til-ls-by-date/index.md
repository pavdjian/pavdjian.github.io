# `ls` by time modified


One of the regular commands I employ when working on the command line is the
`ls` command. A few years ago I set up a couple aliases for `ls` and have used
them exclusively.

```
alias ls='ls -GF'
alias ll='ls -GFlah'
```
Aliasing `ls` to `ls -GF` has given me the ability to list directory contents in
colour and with some applied formatting to quickly distinguish the type of file
presented.

`ll` provides the same functionality as my `ls` alias, but outputs _all_ the directory
contents in their long format, while ensuring their size suffixes are made human
readable.

Recently I found myself wanting to list the contents of a directory chronologically,
how this has never come up before is beyond me, but I cracked open the man page
for `ls` and saw:

```
NAME
     ls -- list directory contents

SYNOPSIS
     ls [-ABCFGHLOPRSTUW@abcdefghiklmnopqrstuwx1%] [file ...]
```

Now that's a whole host of operands, a simple tool it may be, but `ls` packs a
punch 👊🏼

Scanning the operands available to me, I've found what I wanted `t`.
The `t` operand allows me to sort output based on time modified, though I should note
that on a personal level for the output to feel usable to me I need to couple
the output with the `l` operand so that I can actually see the time associated
with each file.

```
~/Downloads
» ls -t
assets     assets.zip
```

```
~/Downloads
» ls -lt
drwxr-xr-x@ 12 pavdjian  1540312140    384 24 Aug 17:33 assets
-rw-r--r--@  1 pavdjian  1540312140  83393 24 Aug 17:33 assets.zip
```

In short, I've ended up with a brand new alias for all
my `ls` needs:
```
alias lt='ls -GFlht'
```

{{< admonition type=tip title=Pro-Tip open=true >}}
You can also use the `T` operand to display time including seconds

```
~/Downloads
» ls -lTt
drwxr-xr-x@ 12 pavdjian  1540312140    384 24 Aug 17:33:09 2020 assets
-rw-r--r--@  1 pavdjian  1540312140  83393 24 Aug 17:33:01 2020 assets.zip
```
{{< /admonition >}}

