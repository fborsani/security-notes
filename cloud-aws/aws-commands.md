# AWS Commands



## Buckets

### Generate a presigned url

Create a presigned resource url. This allows to access the resource even if hosted in a private bucket simply by following the generated URL.

```
aws s3 presign s3://<bucket>/<path to resource> --expires-in 604800
```

For buckets created after It is necessary to provide an endpoint and a region.

```
aws s3 presign s3://<bucket>/<path to resource> --expires-in 604800 --region <region> --endpoint-url <endpoint url>
```
