# NMAP CheatSheet

## NMAP Scans:

ARP Scan                                sudo nmap -PR -sn MACHINE_IP/24
ICMP Echo Scan                          sudo nmap -PE -sn MACHINE_IP/24
ICMP Timestamp Scan                     sudo nmap -PP -sn MACHINE_IP/24
ICMP Address Mask Scan                  sudo nmap -PM -sn MACHINE_IP/24
TCP SYN Ping Scan                       sudo nmap -PS22,80,443 -sn MACHINE_IP/30
TCP ACK Ping Scan                       sudo nmap -PA22,80,443 -sn MACHINE_IP/30
UDP Ping Scan                           sudo nmap -PU53,161,162 -sn MACHINE_IP/30
TCP Connect Scan                        nmap -sT 10.10.116.122
TCP SYN Scan                            sudo nmap -sS 10.10.116.122
UDP Scan                                sudo nmap -sU 10.10.116.122
TCP Null Scan                           sudo nmap -sN 10.10.242.141
TCP FIN Scan                            sudo nmap -sF 10.10.242.141
TCP Xmas Scan                           sudo nmap -sX 10.10.242.141
TCP Maimon Scan                         sudo nmap -sM 10.10.242.141
TCP ACK Scan                            sudo nmap -sA 10.10.242.141
TCP Window Scan                         sudo nmap -sW 10.10.242.141
Custom TCP Scan                         sudo nmap --scanflags URGACKPSHRSTSYNFIN 10.10.242.141
Spoofed Source IP                       sudo nmap -S SPOOFED_IP 10.10.242.141
Spoofed MAC Address                     --spoof-mac SPOOFED_MAC
Decoy Scan                              nmap -D DECOY_IP,ME 10.10.242.141
Idle (Zombie) Scan                      sudo nmap -sI ZOMBIE_IP 10.10.242.141
Run traceroute to target                --traceroute

## Nmap Flags:
-f                                      Fragment IP data into 8 bytes  
-ff                                     Fragment IP data into 16 bytes 
-p-                                     All Ports
-p1-1023                                Scan Ports 1 to 1023
-F                                      100 Most Common Ports
-r                                      Scan Ports in Consecutive Order
-T<0-5>                                 -T0 being the Slowest and T5 the Fastest
--max-rate 50                           Rate <= 50 packets/sec
--min-rate 15                           Rate >= 15 packets/sec
--min-parallelism 100                   At least 100 Probes in Parallel
--source-port PORT_NUM                  specify source port number
--data-length NUM                       append random data to reach given length
--reason                                explains how Nmap made its conclusion
-v                                      verbose
-vv                                     very verbose
-d                                      debugging
-dd                                     more details for debugging
