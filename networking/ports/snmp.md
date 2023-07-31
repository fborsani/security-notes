# 161 - SNMP

## Enumeration

```
nmap -sU --open -p 161 <ip addr>
onesixtyone -c <community keyword file> -i <ip list file>
snmpwalk -c <public|private> -v<1|2c|3> -t <timeout> <ip addr>:<port> 
 
snmpwalk -c <comm string> -v1 -t <timeout> <ip addr> .1                         #Full
snmpwalk -c <comm string> -v1 -t <timeout> <ip addr> 1.3.6.1.4.1.77.1.2.25      #Windows users
snmpwalk -c <comm string> -v1 -t <timeout> <ip addr> 1.3.6.1.2.1.25.4.2.1.2     #Windows processes
snmpwalk -c <comm string> -v1 -t <timeout> <ip addr> 1.3.6.1.2.1.6.13.1.3       #Open TCP ports
snmpwalk -c <comm string> -v1 -t <timeout> <ip addr> 1.3.6.1.2.1.25.6.3.1.2     #Installed software
```

### MIB tree navigation

&#x20;[http://www.oid-info.com/cgi-bin/display?tree=#focus](http://www.oid-info.com/cgi-bin/display?tree=#focus)

### Community strings

* **`public`** Read only functions&#x20;
* **`private`** Read/Write in general

Permissions are not totally controlled by the community string used, some values may be writable even if the public string is used. The reverse is also true, if you try to write a read-only object a `noSuchName` or `readOnly` error is received. In versions 1 and 2/2c if you to use a bad community string the server wont respond.

## OID structure

![](../../.gitbook/assets/SNMP\_OID\_MIB\_Tree.png)

#### Structure example

**`1 . 3 . 6 . 1 . 4 . 1 . 1452 . 1 . 2 . 5 . 1 . 3. 21 . 1 . 4 . 7`**

Here is a breakdown of this address.

* 1 – this is called the ISO and it establishes that this is an OID. This is why all OIDs start with “1”
* 3 – this is called ORG and it is used to specify the organization that built the device.
* 6 – this is the dod or the Department of Defense which is the organization that established the Internet first.
* 1 – this is the value of the internet to denote that all communications will happen through the Internet.
* 4 – this value determines that this device is made by a private organization and not a government one.
* 1 – this value denotes that the device is made by an enterprise or a business entity.

These first six values tend to be the same for all devices and they give you the basic information about them. This sequence of numbers will be the same for all OIDs, except when the device is made by the government.

Moving on to the next set of numbers.&#x20;

* 1452 – gives the name of the organization that manufactured this device.
* 1 – explains the type of device. In this case, it is an alarm clock.
* 2 – determines that this device is a remote terminal unit.

The rest of the values give specific information about the device.

* 5 – denotes a discrete alarm point.
* 1 – specific point in the device
* 3 – port
* 21 – address of the port
* 1 – display for the port
* 4 – point number
* 7 – state of the point
