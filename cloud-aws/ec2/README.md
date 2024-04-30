# EC2

## Connect to instance

### Direct SSH or RDP

Use a standard SSH or RDP connection to reach the instance.

* Requires knowing the Elastic or Private IP of the machine
* The Security Group associated with the instance must allow inbound SSH or RDP connections
* The SSH or RDP service must be configured and running on the machine
* Since the authentication procedure is managed directly on the instance, no AWS credentials or keys are needed

### EC2 Connect

A browser based shell leveraging temporary SSH keys as backed against IAM credentials. Can be accessed from the AWS dashboard or from the url below

* Requires configuration of an agent on the target machine
* In order to use this access method from a remote SSH Client, the SSH port must be reachable from the internet and the user must own valid IAM credentials

Direct URL:

```
https:/<region>.console.aws.amazon.com/ec2-instance-connect/ssh?region=<region>&connType=standard&instanceId=<instance id>osUser=<ec2 user>&sshPort=22#/
```

### AWS SSM

AWS Simple System Management allows to install a package or run a command on a Linux or Windows server. This service can also be used to obtain a shell on the EC2 instance using the AWS Systems Manager Session Manager service.

* Requires configuration of an agent on the target machine
* The EC2 instance must own the correct IAM permissions to enable this service
* The user must own valid IAM user credentials to use this service
* Does not require direct network access to the instance

### **EC2 Serial Console**

This method of authentication allows a user to authenticate on the EC2 instance only by knowing the host's username and password.

* An administrator must enable the EC2 Serial Console for the whole region
* Users on the instance must have a password defined, otherwise it will be impossible to login as them
* Does not require valid IAM User credentials
* Does not require direct network access to the EC2 instance

## IMDS

### IMDS vs IMDSv2

#### Authentication

While in IMDS it is possible to send a request without authentication, IMDSv2 requires to perform an authentication step and obtain a taken before sending API requests.

This code will request a token and store it into a variable. The TTL header can be set to any value in seconds.

```bash
IMDS_TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
```

#### Enable IMDSv2

Enable IMDv2 for current instance

```
aws ec2 modify-instance-metadata-options --instance-id <instance id> --http-tokens required --region <region>
```

One line command with required calls included

```
aws ec2 modify-instance-metadata-options --instance-id $(curl -s http://169.254.169.254/latest/meta-data/instance-id) --http-tokens required --region $(curl -s http://169.254.169.254/latest/meta-data/placement/region)
```

### Get instance information

#### Get current role

{% tabs %}
{% tab title="IMDS" %}
```bash
curl -s http://169.254.169.254/latest/meta-data/iam/security-credentials/ 
```
{% endtab %}

{% tab title="IMDSv2" %}
```bash
curl -s -H "X-aws-ec2-metadata-token: $IMDS_TOKEN" http://169.254.169.254/latest/meta-data/iam/security-credentials/ 
```
{% endtab %}
{% endtabs %}

#### Get instance ID

{% tabs %}
{% tab title="IMDS" %}
```bash
curl -s http://169.254.169.254/latest/meta-data/instance-id
```
{% endtab %}

{% tab title="IMDSv2" %}
```bash
curl -s -H "X-aws-ec2-metadata-token: $IMDS_TOKEN" http://169.254.169.254/latest/meta-data/instance-id
```
{% endtab %}
{% endtabs %}

#### Get instance region

{% tabs %}
{% tab title="IMDS" %}
```bash
curl -s http://169.254.169.254/latest/meta-data/placement/region
```
{% endtab %}

{% tab title="IMDSv2" %}
```bash
curl -s -H "X-aws-ec2-metadata-token: $IMDS_TOKEN" http://169.254.169.254/latest/meta-data/placement/region
```
{% endtab %}
{% endtabs %}

#### Get instance Availability Zone

{% tabs %}
{% tab title="IMDS" %}
```bash
curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone
```
{% endtab %}

{% tab title="IMDSv2" %}
```bash
curl -s -H "X-aws-ec2-metadata-token: $IMDS_TOKEN" http://169.254.169.254/latest/meta-data/placement/availability-zone
```
{% endtab %}
{% endtabs %}

## Cloudshell Enumeration

Network information. Can be filtered by instance id or by entry index

```
aws ec2 describe-network-interfaces    #display all
aws ec2 describe-network-interfaces --filters "Name=attachment.instance-id,Values=<instance id>"
aws ec2 describe-network-interfaces | jq '.NetworkInterfaces[0]'
```
