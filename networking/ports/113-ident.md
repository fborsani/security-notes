# 113 - Ident

Allows to identify the user that is executing a service on a given port. Use the following script to enumerate users. By default nmap will query the ident service when scanning the ports and add the information on the report.

#### Manual query:

Connect to the service using nc and supply the following parameters: the remote service port and the port we are using to connect to the service, then press enter.

```
nc -nv <ip> 113
<service port>,<local port>
```

#### Script query

```
ident-user-enum <ip> <ports> #example: 21 22 80 443
```
