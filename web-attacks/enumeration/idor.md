# IDOR

## DIRB

```
dirb <site url> -r -z <delay ms>                    #non recursive
dirb <site or internal folder url> -z <delay>       #full scan / folder recursive scan
dirb <site or internal folder url> -w               #ignore warnings (to use when not logged in)
```

#### With dictionary

```
dirb <site url> <dictionary>
```

#### Detailed scan

Fine tune + case insensitive

```
dirb <site url> <dict> -if
```

## Gobuster

```
gobuster dir -u <domain> -w <path to dict> -s '<http codes i.e. 200,403>' -ek
gobuster dir -u <domain> -w <path to dict> -s '<http codes i.e. 200,403>' -x <file extension> -ek
```

#### Work via proxy

Only SOCKS 5 is supported

```
gobuster dir -p socks5://127.0.0.1:<proxy port> -u <url> -w <dict> -ek
```

#### Dictionaries for bruteforce

```
ls -alh /usr/share/seclists/Discovery/Web-Content
```

#### Interesting HTTP codes:

* **200 204** OK
* **301 302 307** Redirect (can be ignored for more refined results)
* **401** Login required (can be bruteforced)
* **403** Forbidden (can be used to locate admin pages)
* **500** Server error

## WFUZZ

Will replace any instance of the string FUZZ with an entry from a specified dictionary. Can be used to bruteforce pages, script arguments and search for RFI or SQLi vulnerable arguments. The FUZZ keyword can be inserted in every part of the URL or even concatenated with argument strings.

### Directory traversal

```
wfuzz -w <wordlist> <url>/FUZZ -R 4 --sc BBB
wfuzz -w <wordlist> <url>/FUZZ -R 4 --hc 0    #hide empty responses
```

If the server has a custom message for 404 code, you can use the length of the error message as a parameter to distinguish from missing pages and existing ones even if the server always returns 200 as response.

### GET bruteforce

#### Bruteforce argument name

```
wfuzz -w <wordlist> <url>/?FUZZ=test -R 4 --sc BBB
```

#### Bruteforce GET login

```
 wfuzz -z file,<user list> -z file,<pass list> --sc 200,301,302 <url>?user=FUZZ&pass=FUZ2Z
```

### POST bruteforce

#### bruteforce argument

```
wfuzz -w <wordlist> <url> -d <arg>=FUZZ
```

#### POST form bruteforce

```
wfuzz -w <user list> -w <pass list> --sc BBB -d user=FUZZ,pass=FZ2Z
```

##
