# Attacking APIs

## Bypass IP based filters

It is possible to create a proxy to expose traffic and bypass security checks on API endpoints and resources. This can be achieved using [FireProx](https://github.com/ustayready/fireprox)

### Installation

```
git clone https://github.com/ustayready/fireprox
cd fireprox
pip3 install -r requirements.txt
```

If you are installing the tool on a local machine as opposed to Cloudshell instance, you need to configure AWS account access using `aws configure` or the following commands

```
export AWS_SECRET_ACCESS_KEY=<access key value>
export AWS_ACCESS_KEY_ID=<access key name>
#needed only if current access key is temporary (key starts with ASIA)
export AWS_SESSION_TOKEN=<session token>
```

### Manage gateways

Create gateway

```
python fire.py --command create --url <url to expose>
```

List Gateways

```
python fire.py --command list
```

Delete gateway

```
 python3 fire.py --command delete --api_id <id>
```
