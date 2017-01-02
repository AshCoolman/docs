---
layout: page
type: guide
title: Shell scripting
permalink: /guides/shell-scripting/
---
## Shell scripting


## Tips

### Running multiple programs

Cheatsheet ([source](http://askubuntu.com/questions/334994/which-one-is-better-using-or-to-execute-multiple-commands-in-one-line)):
```
A; B    Run A and then B, regardless of success of A
A && B  Run B if A succeeded
A || B  Run B if A failed
A &     Run A in background.
```

    prog1 & prog2 && fg

This will:

 1. Start `prog1`.
 2. Send it to background, but keep printing its output.
 3. Start `prog2`, and *keep it in foreground*, so you can close it with `ctrl-c`.
 4. When you close `prog2`, you'll return to `prog1`'s *foreground*, so you can also close it with `ctrl-c`.
