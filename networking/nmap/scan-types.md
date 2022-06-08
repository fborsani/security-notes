# Scan types

## -sS

* Quick and lightweigth since the handshake is not completed
* Allow to differentiate between opened, filtered and closed ports
* Default when nmap is run as root

## -sT

* Less efficient than -sS since it has to wait for the whole TCP handshake to complete
* Leaves traces in target's logfiles
* Default when nmap is not run with root privileges
* Guaranteed to work with proxychains

## -sU

* UDP scan, can be combined with TCP ones
* Very slow since most systems limit the number of responses per second
* Better prioritize the most used ports instead of scanning all 65535

## -sY

* SCTP equivalent of the TCP Syn scan
* Fast and efficient

## -sZ

* SCTP equivalent of -sN scan
* Stealthier and reliable
* Cannot distinguish between open and filtered ports

## -sN; -sF; -sX

* Exploit a loophole in RFC that can be used to sneak through IDS
* More chance of hiding the attack from detection
* The effectiveness of the scan depends on the implementation of the RFC 789 on the target machine
* More reliable against Unix-based systems
* Cannot distinguish open ports from filtered ones

## -sM

* Works like the -sN scan, but with ACK/FIN flags
* Many BSD-based systems simply drop the packet

## -sA

* Is used to map firewall rulesets, finding out which ports are filtered
* All reachable ports are mapped as unfiltered
* Ports that are unreachable or send back certain ICMP error messages are mapped as filtered
* Unable to determine if a port is open or closed

## -sW

* Works the same way as the -sA scan
* Is also able to differentiate between open and closed ports depending on the size of their window (0 if port is closed)
* Unreliable, only a minority of all devices implement the window in this way
* System that don't support this scan report all ports as closed
* If you find lots of closed ports and just a handful of filtered ones, than the system might be suscectible, even the reverse is true: if you find lots of filtered ports and just a couple of closed ones then they might actually be open

## -s0

* Allows to detect which IP protocols run on a given port
* Certain systems expect custom headers so some ports might be flagged as closed or open|filtered
* Does not test for TCP, UDP, ICMP, SCTP, and IGMP



