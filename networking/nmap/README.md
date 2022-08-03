---
description: nmap cheatsheet
---

# Nmap

## Tool

### Scope

```
nmap <host list>
nmap -6 <host list>
nmap -iL <path to file>.txt
nmap <host list> --exclude <host list>
```

### Host discovery

```
-sL    No Scan. List targets only
-sn    Disable port scanning. Host discovery only.
-Pn    Disable host discovery. Port scan only.
-PS    TCP SYN discovery on port x. Port 80 by default
-PA    TCP ACK discovery on port x. Port 80 by default
-PU    Port 40125 by default
-PR    ARP discovery on local network
-n     Never do DNS resolution
```

### Scan options

```
-sS    TCP SYN port scan (Default)
-sT    TCP connect port scan (Default without root privilege)
-sU    UDP port scan (Can be combined with other scan options)
-sA    TCP ACK port scan
-sW    TCP Window port scan
-sM    TCP Maimon port scan
-sN    TCP NULL port scan
-sF    TCP FIN port scan
-sX    TCP Xmas port scan
-sY    SCTP INIT scan
-sZ    COOKIE-ECHO scan
```

### Version & OS

```
-sV                    Attempts to determine the version of the service running on port
-sV --version-light    Enable light mode. Lower accuracy. Faster
-sV --version-all      Higher accuracy. Slower
-O		       Remote OS detection using TCP/IP stack fingerprinting
-O --osscan-limit      If at least one open and one closed TCP port are not found it will not try OS detection
-O --osscan-guess      Makes Nmap guess more aggressively
-A                     Traceroute, version and OS scan
```

### Ports

```
-p             Use port range, protocol or mixed i.e. T:443,U:1040
-p 1-65535     All ports
--top-ports    Most used ports
-F	       Top 100 ports
```

### Evasion

```
-f              Requested scan (including ping scans) use tiny fragmented IP packets
--mtu           Set your own offset size
-D              Send scans from spoofed IPs
-S              Spoof host (-e eth0 -Pn may be required)
--spoof-mac     Spoof NMAP MAC address
--badsum        Send packets with a bogus TCP/UDP/SCTP checksum
-g	        Use given source port number
--proxies       Relay connections through HTTP/SOCKS4 proxies
--data-length	Appends random data to sent packets
```

### Output

```
-oN			File.txt
-oX			File.xml
-oG			Grepable
-oA			Output in the three major formats at once
```

## Templates

#### Common services slow

```
nmap -Pn -p 21,22,25,53,80,110,111,143,443,445,1433,1521,3306,5432,8080,8443 -T2 <ip> -A -vvv -oA ./nmap
```

#### Service detailed scan

```
nmap -Pn -p <port> <ip> -sV --version-all -sC -vvv -oA ./nmap
```

#### Full scan

```
nmap -Pn -p- <ip> -sV --version-all -A -vvv -oA ./nmap
```

#### Light scan

```
nmap -Pn --top-ports 500 <ip> -vvv -oA ./nmap
```

#### Machine discovery

```
nmap -PR -T2 --top-ports 100 <ip> -vvv -oA ./nmap
```

#### Machine discovery no scan

```
nmap -PR -T2 -sn <ip> -vvv -oA ./nmap
```
