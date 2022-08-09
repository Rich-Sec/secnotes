# Linux PrivEsc CheatSheet
[Home](../../index.md) | [CheatSheets](../../cheatsheets.md) | [Theory](../../theory.md) | [About](../../about.md) | [Back](../linuxprivesc.md)

## Shell Stabilization:

| Command                                        | Purpose                                                                                                                                                                                       |
|------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| python -c "import pty; pty.spawn('/bin/bash')" | Upgrade from sh by spawning a bash shell using python / python3                                                                                                                               |
| ruby -e "exec '/bin/bash'"                     | Upgrade to bash shell using ruby                                                                                                                                                              |
| perl -e "exec '/bin/bash';"                    | Update to bash shell using perl                                                                                                                                                               |
| stty raw -echo && fg                           | Background the reverse shell process first using ctrl+z and then run this command on your attacking machine. Improves the stability of the shell when sending characters over the connection. |
