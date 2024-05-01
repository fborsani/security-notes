# Expose Resources

## Assign public IP address

Generate public address. Store the allocation ID because it will be needed in following commands

```
aws ec2 allocate-address
```

Get ENI Attachment IDs of EC2 instances in VPC

```
aws ec2 describe-instances --query 'Reservations[].Instances[].[InstanceId, NetworkInterfaces[].[Attachment.AttachmentId, NetworkInterfaceId]]' --output text
```

Assign public IP to attached ENI. The required IDs are the ENI (eni-xxxx) and the IP allocation one (eipalloc-xxxxxxx) returned by the first command

```
 aws ec2 associate-address --network-interface-id <interface id>  --allocation-id <allocation id>
```

## Modify Routing Table to allow public traffic

Get a list of available gateways in current VPC. Save the ID of a gateway to use to route traffic outside the VPC

```
aws ec2 describe-internet-gateways
```

Get routing tables

```
aws ec2 describe-route-tables
```

Edit the routing table to allow traffic from the VPC to the internet

```
 aws ec2 create-route --route-table-id <route table id> --destination-cidr-block 0.0.0.0/0 --gateway-id <gateway id>
```

## Disable instance ACL rules

Get the security groups associated with each EC2 instance. Edit the one of the instance you intend to expose

```
aws ec2 describe-instances --query "Reservations[].Instances[].[InstanceId, NetworkInterfaces[].Groups[*]]" --output text
```

Add inbound rule to security group to allow all traffic to reach the instance

```
aws ec2 authorize-security-group-ingress  --protocol all --port 0-65535 --cidr 0.0.0.0/0 --group-id <group id>
```

## Disable NACL rules

Get the VPC of the target EC2 instance

```
aws ec2 describe-instances --query "Reservations[].Instances[].[InstanceId, VpcId]" --output text
```

Get NACL rules for the vpc. Dependingon the rules it may be required to insert new rules to allow all traffic inbound and/or outbound in one or more NACL instances. Keep in mind that NACL are stateless so it is possible that inbound traffic is allowed but the response is still being blocked by the outbound filter rules. in this case we need to add an allow all rule to the outbound traffic too.

```
aws ec2 describe-network-acls --filter "Name=vpc-id,Values=<vpc id>"
```

Add rule to allow all inbound traffic

```
aws ec2 create-network-acl-entry --cidr-block 0.0.0.0/0 --ingress  --protocol -1 --rule-action allow --rule-number 1 --network-acl-id <acl id>
```

Add rule to allow all outbound traffic

```
aws ec2 create-network-acl-entry --cidr-block 0.0.0.0/0 --egress --protocol -1 --rule-action allow --rule-number 1 --network-acl-id <acl id>
```
