# DNS Recon CheatSheet
[Home](index.md) | [CheatSheets](cheatsheets.md) | [Theory](theory.md) | [About](about.md)

| Purpose                               | Command                                          |
| ------------------------------------- | :---------------------------------------------:  |
|   Lookup WHOIS record                 |    whois :IP:                                   |             
|   Lookup DNS A records                |    nslookup -type=A :IP:                        |          
|   Lookup DNS MX records at DNS server |    nslookup -type=MX :IP: 1.1.1.1               | 
|   Lookup DNS TXT records              |    nslookup -type=TXT :IP:                      | 
|   Lookup DNS A records                |    dig :IP: A                                   | 
|   Lookup DNS MX records at DNS server |    dig @1.1.1.1 :IP: MX                         | 
|   Lookup DNS TXT records              |    dig :IP: TXT                                 | 
