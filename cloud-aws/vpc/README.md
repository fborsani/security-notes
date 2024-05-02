# VPC

## Structure

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### Global services

* Cludfront
* Route 53
* IAM
* Organizations
* STS

### Regional architecture

* **Region:** a physical location in the world where a cluster of data centers is present
  * **Availability Zone:** one or more discrete data centers with redundant power, networking, and connectivity in an AWS Region. These separations mean that an outage of one AZ due to lost power, networking issues, or ISP connectivity issues should not affect any other AZ
    * S3 Bucket
    * VPC
      * Gateway
      * Amazon DNS resolver instance
      * Amazon Time Sync Service
      * Microsoft KMS Service
      * Elastic IP Address range
      * EC2 Instance Metadata Service
      * ECS Task Metadata Service
      * EC2 Instances
      * VPC Endpoints

## Enumeration

### VPC Elements

Public load balancers (ELB)

```
aws elbv2 describe-load-balancers --query LoadBalancers[].DNSName --output text
```

Endpoints and Managed Prefix List

```
aws ec2 describe-prefix-lists
```

### Route 53

Hosted zones

```
aws route53 list-hosted-zones
```

DNS records

```
aws route53 list-resource-record-sets --hosted-zone <hosted zone id>
```

NS Lookup

```
aws route53 list-resource-record-sets --hosted-zone <zone id> --query "ResourceRecordSets[?Type=='A' && contains(Name,'<domain name>')].ResourceRecords[*].Value"
```

Reverse NS Lookup

```
aws route53 list-resource-record-sets --hosted-zone /hostedzone/Z07539222LGSNXAUJI1RU --query "ResourceRecordSets[?Type=='A' && ResourceRecords[?Value=='<ip address>']].Name
```
