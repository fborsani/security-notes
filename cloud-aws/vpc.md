# VPC

## Enumeration

Public load balancers (ELB)

```
aws elbv2 describe-load-balancers --query LoadBalancers[].DNSName --output text
```
