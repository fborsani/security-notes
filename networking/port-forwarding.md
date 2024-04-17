# Port forwarding

## Proxychains-ng

Version of proxychains that allows to specify different configurations for each connection. Useful for running multiple proxies on different ports when pivoting through several subnets.

```
apt-get install proxychains4
proxychains -f <conf file> <command>
```

## SSH

### Local port forwarding

Allow to reach an isolated machine connected to an exploited one by using the exploited machine to forward all packages between the Kali host and the target.

**On Kali:** redirect any traffic (0.0.0.0) to the target by logging in the compromised machine in the middle

```
ssh -N -L <origin ip>:<origin port>:<target ip>:<target port> <middle usr>@<middle ip>
```

### Remote port forwarding

Use this technique if the firewall is blocking incoming connections so it is not possible to establish an SSH session between the attacking machine and the target, but we can establish a connection from the targeted machine towards our host bypassing the firewall’s inbound traffic filters.

Can also be used to expose services running as localhost on the remote machine to the attacker, to the remote machine all connections being forwarded will appear as if they were coming from localhost.

**On compromised machine:** reroute all incoming traffic to attacking machine

```
ssh -N -R <kali ip>:<kali port>:<target address>:<target port> <kali user>@<kali ip>
```

#### Example of exposing services running as localhost

Run the following command on the compromised machine to map the local service port to a new port exposed to the attacker's machine.

```
ssh -N -R <exposed port>:localhost:<service port> <kali user>@<kali ip>
```

We can access the exposed service by navigating to the exposed port in our attacker machine. For instance if the exposed service is a web server and the specified exposed port is 8000 we can access the hosted content by navigating to `http://localhost:8000` on the attacker machine

### Dynamic port forwarding

Set a local listening port and have it tunnel incoming traffic to any remote destination through a proxy. Works the same way as a local port forwarding but allows to target different ports and machines without having to create different tunnels for each host or port.

**On Kali:** connect us (127.0.0.1) to any target in network through the machine in the middle

```
ssh -N -D 127.0.0.1:<port> <middle user>@<middle ip>
```

#### Setup:

```
cat /etc/proxychains.conf
#new proxies go under [ProxyList]
socks4 127.0.0.1 <port>
#run commands through proxy
proxychains <command>
```

### Reverse dynamic port forwarding

Works the same way as the Remote port forwarding technique plus the ability to change destination on the target network.

**On compromised machine:** reroute all traffic from any port to the attacker

```
ssh -f -N -R <port> -o "UserKnownHostsFile=/dev/null" -o "StrictHostKeyChecking=no" -i <path to keys> <kali user>@<kali ip>
```

#### Setup:

1\. Create public keys on compromised machine

```
mkdir /tmp/keys
ssh-keygen
cat <path to key>.pub
```

2\. Copy the keys in your kali machine

```
echo from="<domain ip>",command="echo 'This account can only be used for port forwarding'",no-agent-forwarding,no-X11-forwarding,no-pty ssh-rsa ssh-rsa <key.pub content> >> ~/.ssh/authorized_keys
```

3\. configure proxychains

```
cat /etc/proxychains.conf
socks4 <proxy ip> <port>
#run commands through proxy
proxychains <command>
```
