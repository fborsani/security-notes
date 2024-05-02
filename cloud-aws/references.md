# References

## ARN Structure

Generic structure

```
arn:partition:service:region:account-id:resource-id
arn:partition:service:region:account-id:resource-type/resource-id
arn:partition:service:region:account-id:resource-type:resource-id
```

* Partition: can be one of the following: `aws`, `aws-cn` (Chinese branch), `aws-us-gov` (US Gov restricted access)
* [Service](references.md#services)
* [Region](references.md#regions)
* Account-id: account id of the resource owner
* Resource-id: id code of the specified resource or service
* Resource-type: some resource categories allow more granularity whensearching for a particular resource. For instance EC2 services can be subdivided into instances, images, security groups and so on

## Services

The following repository contains a list of services grouped by resource. The list is generated from the AWS documentation

{% embed url="https://github.com/fborsani/aws-service-list/blob/main/generated_docs/aws_services.md" %}

## Regions

<table><thead><tr><th width="239">Code</th><th>Name</th></tr></thead><tbody><tr><td>us-east-2</td><td>US East (Ohio)</td></tr><tr><td>us-east-1</td><td>US East (Virginia)</td></tr><tr><td>us-west-1</td><td>US West (N. California)</td></tr><tr><td>us-west-2</td><td>US West (Oregon)</td></tr><tr><td>ap-south-1</td><td>Asia Pacific (Mumbai)</td></tr><tr><td>ap-northeast-3</td><td>Asia Pacific (Osaka)</td></tr><tr><td>ap-northeast-2</td><td>Asia Pacific (Seoul)</td></tr><tr><td>ap-southeast-1</td><td>Asia Pacific (Singapore)</td></tr><tr><td>ap-southeast-2</td><td>Asia Pacific (Sydney)</td></tr><tr><td>ap-northeast-1</td><td>Asia Pacific (Tokyo)</td></tr><tr><td>ca-central-1</td><td>Canada (Central)</td></tr><tr><td>eu-central-1</td><td>Europe (Frankfurt)</td></tr><tr><td>eu-west-1</td><td>Europe (Ireland)</td></tr><tr><td>eu-west-2</td><td>Europe (London)</td></tr><tr><td>eu-west-3</td><td>Europe (Paris)</td></tr><tr><td>eu-north-1</td><td>Europe (Stockholm)</td></tr><tr><td>sa-east-1</td><td>South America (São Paulo)</td></tr></tbody></table>

Opt-in regions

<table><thead><tr><th width="240">Code</th><th>Name</th></tr></thead><tbody><tr><td>af-south-1</td><td>Africa (Cape Town)</td></tr><tr><td>ap-east-1</td><td>Asia Pacific (Hong Kong)</td></tr><tr><td>ap-south-2</td><td>Asia Pacific (Hyderabad)</td></tr><tr><td>ap-southeast-3</td><td>Asia Pacific (Jakarta)</td></tr><tr><td>ap-southeast-4</td><td>Asia Pacific (Melbourne)</td></tr><tr><td>ca-west-1</td><td>Canada West (Calgary)</td></tr><tr><td>eu-south-1</td><td>Europe (Milan)</td></tr><tr><td>eu-south-2</td><td>Europe (Spain)</td></tr><tr><td>eu-central-2</td><td>Europe (Zurich)</td></tr><tr><td>il-central-1</td><td>Israel (Tel Aviv)</td></tr><tr><td>me-south-1</td><td>Middle East (Bahrain)</td></tr><tr><td>me-central-1</td><td>Middle East (UAE)</td></tr></tbody></table>

## ID Prefixes

| Prefix | Resource type                                                                                                                                                                                                         |
| ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ABIA   | [AWS STS service bearer token](https://docs.aws.amazon.com/IAM/latest/UserGuide/id\_credentials\_bearer.html)                                                                                                         |
| ACCA   | Context-specific credential                                                                                                                                                                                           |
| AGPA   | User group                                                                                                                                                                                                            |
| AIDA   | IAM user                                                                                                                                                                                                              |
| AIPA   | Amazon EC2 instance profile                                                                                                                                                                                           |
| AKIA   | Access key                                                                                                                                                                                                            |
| ANPA   | Managed policy                                                                                                                                                                                                        |
| ANVA   | Version in a managed policy                                                                                                                                                                                           |
| APKA   | Public key                                                                                                                                                                                                            |
| AROA   | Role                                                                                                                                                                                                                  |
| ASCA   | Certificate                                                                                                                                                                                                           |
| ASIA   | [Temporary (AWS STS) access key IDs](https://docs.aws.amazon.com/STS/latest/APIReference/API\_Credentials.html) use this prefix, but are unique only in combination with the secret access key and the session token. |
