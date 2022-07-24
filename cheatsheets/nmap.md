# NMAP CheatSheet
[Home](../index.md) | [About](../about.md) | [CheatSheets](../cheatsheets.md)

| Scan Type               | Command                                        |
| ----------------------- | :---------------------------------------------:|
| ARP Scan                | sudo nmap -PR -sn <IP>/24                      |
| ICMP Echo Scan          | sudo nmap -PE -sn <IP>/24                      |
| ICMP Timestamp Scan     | sudo nmap -PP -sn <IP>/24                      |
| ICMP Address Mask Scan  | sudo nmap -PM -sn <IP>/24                      |
| TCP SYN Ping Scan       | sudo nmap -PS22,80,443 -sn <IP>/30             |
| TCP ACK Ping Scan       | sudo nmap -PA22,80,443 -sn <IP>/30             |
| UDP Ping Scan           | sudo nmap -PU53,161,162 -sn <IP>/30            | 
| TCP Connect Scan        | nmap -sT <IP>                                  |
| TCP SYN Scan            | sudo nmap -sS <IP>                             |
| UDP Scan                | sudo nmap -sU <IP>                             |
| TCP Null Scan           | sudo nmap -sN <IP>                             |
| TCP FIN Scan            | sudo nmap -sF <IP>                             |
| TCP Xmas Scan           | sudo nmap -sX <IP>                             |
| TCP Maimon Scan         | sudo nmap -sM <IP>                             |
| TCP ACK Scan            | sudo nmap -sA <IP>                             |
| TCP Window Scan         | sudo nmap -sW <IP>                             |
| Custom TCP Scan         | sudo nmap --scanflags URGACKPSHRSTSYNFIN <IP>  |
| Spoofed Source IP       | sudo nmap -S SPOOFED_IP <IP>                   |
| Spoofed MAC Address     | --spoof-mac SPOOFED_MAC                        |
| Decoy Scan              | nmap -D DECOY_IP,ME <IP>                       |
| Idle (Zombie) Scan      | sudo nmap -sI ZOMBIE_IP <IP>                   |  


| Flag                                    | Purpose                                  |
| --------------------------------------- | :---------------------------------------:|
|-f                                       |  Fragment IP data into 8 bytes           |
|-ff                                      |  Fragment IP data into 16 bytes          |
|-p-                                      |  All Ports                               | 
|-p1-1023                                 |  Scan Ports 1 to 1023                    |
|-F                                       | 100 Most Common Ports                    |
|-r                                       | Scan Ports in Consecutive Order          |
|-T<0-5>                                  | -T0 being the Slowest and T5 the Fastest |
|--max-rate 50                            | Rate <= 50 packets/sec                   |
|--min-rate 15                            | Rate >= 15 packets/sec                   |
|--min-parallelism 100                    | At least 100 Probes in Parallel          | 
|--source-port PORT_NUM                   | Specify source port number               |
|--data-length NUM                        | Append random data to reach given length |
|--traceroute                             | Run traceroute to target                 |
|--reason                                 | explains how Nmap made its conclusion    |  
|-v                                       | verbose                                  |
|-vv                                      | very verbose                             |
|-d                                       | debugging                                |
|-dd                                      | more details for debugging               |
