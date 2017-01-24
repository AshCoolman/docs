---
layout: page
type: guide
title: Shell scripting
permalink: /guides/shell-scripting/
---
## Shell scripting

### Quick tips

```sh
echo $? # get status code of previous command
```

### Running multiple programs

Cheatsheet ([source](http://askubuntu.com/questions/334994/which-one-is-better-using-or-to-execute-multiple-commands-in-one-line)):

```sh
A; B    # Run A and then B, regardless of success of A
A && B  # Run B if A succeeded
A || B  # Run B if A failed
A &     # Run A in background.
```

For example:

```sh
    prog1 & prog2 && fg
```

 1. Start `prog1`.
 2. Send it to background, but keep printing its output.
 3. Start `prog2`, and *keep it in foreground*, so you can close it with `ctrl-c`.
 4. When you close `prog2`, you'll return to `prog1`'s *foreground*, so you can also close it with `ctrl-c`.

*TIP*: `&&` is a good way to ["and" cmd exit codes](http://stackoverflow.com/questions/5195607/checking-bash-exit-status-of-several-commands-efficiently)

### Empty strings

Is empty
```sh
[[ -z  $param  ]] # Bash keyword version
[ -z  "$param"  ] # more portable shell version
[ !  -z  "$param"  ] # Negated
```

## OSX

### Securly delete a file

```sh
# http://www.macworld.com/article/3005796/operating-systems/how-to-replace-secure-empty-trash-in-os-x-el-capitan.html
srm -zsv /path/to/file
```
## Resources

* [Brackets: Double vs single](http://serverfault.com/questions/52034/what-is-the-difference-between-double-and-single-square-brackets-in-bash)