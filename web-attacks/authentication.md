# Authentication

## Implementation errors

### Access control bad practices

* **Unprotected functionality**: common users have access to a page that should be restricted (i.e. an admin page)
* **Location based access control**: access control based on geolocalization can be easily spoofed by using proxies or VPNs
* **Referer based access control**: access control is validated by analyzing the `referer` header that is used to indicate the page from which the user has sent a request from. For instance if a user is arriving from an administrative page then he is considered and admin. This header as all parameters of an HTTP request can be easily spoofed or tampered,
* **Parameter based access control**: access control is validated by reading the parameters associated with a GET or POST request. These parameters can be easily tampered or removed
* **HTTP dialect based access control**: some applications deny requests from certain users based on user type, resource requested and type of HTTP request. These controls can be bypassed by using a different verb for instance sending a GET request instead of a POST. It is also possible to rewrite the destination URL by adding headers such as `X-Original-URL` and `X-Rewrite-URL`
* **Horizontal privilege escalation:** a user is capable of accessing resources belonging to other users of the same group. For instance a user may be capable of access not only his account page but also the account page of any other user by modifying the request's parameters
* **Vertical privilege escalation:** a user is capable of impersonating another user with greater permissions such as a site administrator by tampering the request's parameters. Can be a consequence of horizontal privilege escalation.
* **IDOR:** an Insecure Direct Object Request happens when an application uses user supplied input to access objects directly without verifying whether the current user is allowed to access the given resource

### Authentication bad practices

* **Use of HTTP basic auth**: HTTP basic auth is vulnerable to wide array of attacks such as CSRF and XSS. It may also be possible to intercept the user's credentials
* **Vulnerable to bruteforcing**: websites that rely exclusively on username and password login should include anti-bruteforce measures such as Captcha fields and two factor authentication systems in order to prevent attackers from guessing valid credentials
* **Error based account enumeration**: if the application has a different behavior in case of a login attempt with an existing username and wrong password (as opposed to a login attempt with a non existing username) then it is possible for an attacker to harvest existing user account names by analyzing differences in the server's response or timing
* **DOS through flawed anti-bruteforce measures**: anti-bruteforce measures that involve temporary or permanent account blocks and password resets can be exploited by attackers to prevent legitimate users from logging in
* **Second authentication step can be bypassed**: in some cases after an user has submitted his credentials and is redirected to a different page to complete the verification process he is considered logged in despite not having yet completed the second step. This allows an attacker to bypass the second step by directly navigating to a page only accessible to an authenticated user
* **2FA token can be bruteforced**: since many authentication tokens are very simple in nature (6-8 digits) it can be easy for an attacker to bruteforce the token. Logging out the user after a given number of wrong tokens is not an effective solution because and attacker can automate the whole login process including the part where the user inserts his credentials
* **Guessable remember me cookie**: predictable remember me tokens generated from guessable data (for instance the username concatenated with a timestamp) are a great security risk because an attacker able to forge a remember me token can effectively skip the whole login process

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
