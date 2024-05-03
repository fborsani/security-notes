# Public Bucket

## Enumeration

### Google Dorks

Generic search. Chain these search parameters with target specific names or keywords to detect only the buckets within the test scope

```
site:http://s3.amazonaws.com intitle:index.of.bucket
site:.s3.amazonaws.com "<org name>"
site:http://amazonaws.com inurl:".s3.amazonaws.com/"
intitle:index.of.bucket
site:http://s3.amazonaws.com intitle:Bucket loading
site:*.amazonaws.com inurl:index.html
Bucket Date Modified
```

### Domain recon

DNS Query

```
nslookup <domain>    #try with top domain and subdomain
```

### Default names

```
http://s3.amazonaws.com/bucket/key (for a bucket created in the US East (N. Virginia) region)
https://s3.amazonaws.com/bucket/key
http://s3-region.amazonaws.com/bucket/key
https://s3-region.amazonaws.com/bucket/key
http://s3.region.amazonaws.com/bucket/key
https://s3.region.amazonaws.com/bucket/key
http://s3.dualstack.region.amazonaws.com/bucket/key (for requests using IPv4 or IPv6)
https://s3.dualstack.region.amazonaws.com/bucket/key
http://bucket.s3.amazonaws.com/key
http://bucket.s3-region.amazonaws.com/key
http://bucket.s3.region.amazonaws.com/key
http://bucket.s3.dualstack.region.amazonaws.com/key (for requests using IPv4 or IPv6)
http://bucket.s3-website.region.amazonaws.com/key (if static website hosting is enabled on the bucket)
http://bucket.s3-accelerate.amazonaws.com/key (where the filetransfer exits Amazon's network at the last possible moment so as to give the fastest possible transfer speed and lowest latency)
http://bucket.s3-accelerate.dualstack.amazonaws.com/key
http://bucket/key (where bucket is a DNS CNAME record pointing to bucket.s3.amazonaws.com)
https://access_point_name-account ID.s3-accesspoint.region.amazonaws.com (for requests via an access point granting restricted access to a bucket)
```

Other typical name conventions

```
assets.<domain>.com.s3.amazonaws.com/
<domain>-<prod|uat|test|dev|public>.s3.amazonaws.com/
```

## Dump Bucket

```
aws s3 sync s3://<bucket> . --no-sign-request    #all
aws s3 sync s3://<bucket>/<path> . --no-sign-request    #all from specified path
```
