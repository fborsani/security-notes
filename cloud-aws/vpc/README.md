# VPC

## Structure

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

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
