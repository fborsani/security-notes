# Enumeration

## Instances

List all instances

```
aws ec2 describe-instances --query 'Reservations[*].Instances[*].[Tags[?Key == `Name`].Value,InstanceId,State.Name,InstanceType,PublicIpAddress,PrivateIpAddress]' --output text | sed 'N;s/\n/ /'
```

Download the UserData script file from all instances

```
ec2_list=$(aws ec2 describe-instances --region us-east-1 --query Reservations[].Instances[].InstanceId --output text --profile ec2-capstone)
for i in $ec2_list; do
  aws ec2 describe-instance-attribute --profile ec2-capstone --instance-id $i --attribute userData --output text --query UserData --region us-east-1 | base64 --decode > $i-USERDATA.txt
done
```

## Network

```
aws ec2 describe-network-interfaces    #display all
aws ec2 describe-network-interfaces --filters "Name=attachment.instance-id,Values=<instance id>"
aws ec2 describe-network-interfaces | jq '.NetworkInterfaces[0]'
```
