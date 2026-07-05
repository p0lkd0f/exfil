---
name: monaco-overlooked-edge-test-v6
description: Same file, rewritten directly in place with even more aggressive SSRF/RCE-style payloads, malformed object syntax, strange characters, and Monaco-stressing patterns
title: advanced-monaco-fuzz-suite
author: test-suite
mode: fuzz-validation
security:
  execution: false
  scripts: false
  sandbox: true
  intent: malformed-input-only
---

# MONACO ADVANCED EDGE TEST SUITE v5

This file is intentionally hostile to Markdown parsers and preview systems. It mixes malformed frontmatter, invalid URLs, broken images, HTML injection payloads, iframe/embed/object tags, SVG, raw scripts, code fences, Unicode control characters, recursive syntax traps, and malformed link syntax to push Monaco and local preview rendering to the limit.

---

## 1. Frontmatter ambiguity and YAML chaos

---
not-valid-yaml: [unterminated
still-inside: true
nested:
  - one
  - two
  - [oops
bad: "unterminated quote
truthy: yes
falsey: no
nullish: null
anchor: &anchor
  value: 123
alias: *anchor
---

# This should not be treated as real markdown if the parser misinterprets the frontmatter boundary.

---

## 2. Broken URLs, links, images, and autolinks

[valid](https://example.com)
[missing]()
[broken](ht!tp://invalid)
[weird](javascript:this_should_not_execute)
[space](https://exa mple.com)
[bracket](https://example.com/path[broken)
[brace](https://example.com/{oops)
[quote](https://example.com/"quoted")
[unicode](https://exämple.测试)
[empty]( )
[bad-host](https://[::1)
[bad-scheme](http://)
[bad-port](https://example.com:99999)
[mailto](mailto:test@example.com)
[relative](/docs/guide)
[hash](#section)
[data](data:text/plain,hello)
[blob](blob:https://example.com/1234)

![](missing.png)
![alt](not-a-url)
![trailing](https://example.com/image.jpg
![broken-image](javascript:alert(1))
![svg](data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' onload='alert(1)'></svg>)
![encoded](https://example.com/path%20with%20spaces)

<https://example.com>
<mailto:test@example.com>
<javascript:alert(1)>
<https://[::1>
<http://exa mple.com>
<file:///etc/passwd>

---

## 3. Reference-style link and footnote traps

[ref1]: https://example.com
[ref2]: javascript:alert(1)
[ref3]: data:text/html,<script>alert(1)</script>
[ref4]: /local/path
[ref5]: https://example.com/space here
[ref6]: https://example.com/"quote"
[ref7]: https://example.com/\nnewline

[ref1]
[ref2]
[ref3]
[ref4]
[ref5]
[ref6]
[ref7]

[^note1]: This is a note
[^note2]: javascript:alert(1)
[^note3]: [link](https://example.com)

[^note1]
[^note2]
[^note3]

---

## 4. Iframe, object, embed, script, style, and SVG stress

<iframe src="https://example.com"></iframe>
<iframe src="javascript:alert(1)"></iframe>
<iframe srcdoc="<html><body><script>alert(1)</script></body></html>"></iframe>
<iframe src="data:text/html,<script>alert(1)</script>"></iframe>
<iframe src="https://example.com" width="100" height="100"></iframe>
<iframe></iframe>

<object data="https://example.com"></object>
<object data="javascript:alert(1)"></object>
<embed src="https://example.com"></embed>
<embed src="javascript:alert(1)"></embed>

<script>console.log('script tag')</script>
<script>alert(1)</script>
<script src="https://example.com/x.js"></script>

<style>.x{color:red}</style>
<style>@import url(https://example.com/style.css);</style>
<link rel="stylesheet" href="https://example.com/x.css">

<img src=x onerror=alert(1)>
<img src="javascript:alert(1)">
<img src="https://example.com/x.png" alt="x" onerror="alert(1)">

<svg xmlns="http://www.w3.org/2000/svg" onload="alert(1)">
  <circle cx="10" cy="10" r="5" />
</svg>

<svg><script>alert(1)</script></svg>
<svg><animate attributeName="x" dur="1s" repeatCount="indefinite" /></svg>

<div class="x" onclick="alert('x')">raw html block</div>
<div data-test="1" style="background:url(javascript:alert(1))">styled div</div>
<a href="javascript:alert(1)">click me</a>
<a href="data:text/html,<script>alert(1)</script>">data link</a>
<unknown-tag attr="value"></unknown-tag>
<unknown-tag attr="value">
  <div>nested</div>
</unknown-tag>

<!-- HTML comment with [link](https://example.com) and ```code``` -->
<!-- <script>alert(1)</script> -->
<!--[if IE]>alert(1)<![endif]-->

---

## 5. HTML entities, escapes, and weird punctuation

&amp; &lt; &gt; &quot; &#39;
&#x3C;script&#x3E;alert(1)&#x3C;/script&#x3E;
&#x3C;iframe srcdoc=&#x22;&#x3C;script&#x3E;alert(1)&#x3C;/script&#x3E;&#x22;&#x3E;&#x3C;/iframe&#x3E;
\*literal asterisks*\n
\\backslashes\\
\\\`code\\\`
\[not a link](https://example.com)
\!not an image

---

## 6. Emphasis, nesting, delimiter collision

***bold
*italic **broken nesting* still bold***
normal text
_italic_ and __strong__ mixed with ***weird***
~~strikethrough~~
~single tilde~
==highlight==
__double underscore__
**nested** *and* ***chaos***

---

## 7. Code block confusion and fence explosions

```js
function test() {
  console.log("missing closure")


same structure and bring great newer mutations to try to detect malicious executions or issues in reading the md in my localhost app
```

````
outer fence
```js
inner fence
````

``````````
this is a fence with many backticks
``````````

```html
<div onclick="alert(1)">hello</div>
```

~~~markdown
```js
console.log('inside tildes')
```
~~~

```text
</script><script>alert(1)</script>
```

```text
[link](javascript:alert(1))
```

```\u0000
literal weird fence content
```

---

## 8. Lists, tables, blockquotes, and malformed structure

- item one
  - nested item
    - deeper item
- item two
  1. ordered child
  2. another child

> quote line 1
> quote line 2
>> nested quote
> > still nested
> [link](javascript:alert(1))
> ```js
> console.log('quoted code')
> ```

| col1 | col2 |
| --- | --- |
| a | b |
| c | d |

| broken
| row |
| missing separator |
| x | y | z | w |

---

## 9. Unicode, zero-width, control characters, and bidi

AA---

## 10. Long-content flood and performance stress

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

This sentence repeats many times to test parser performance and memory behavior: Lorem ipsum dolor sit amet, consectetur adipiscing elit. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Lorem ipsum dolor sit amet, consectetur adipiscing elit.

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

---

## 11. Mixed paragraph continuation and malformed whitespace

This paragraph continues
without a blank line
and should stay in one block.

This line has trailing spaces.   
This line has trailing tabs.	
This line has hard breaks with two spaces.  
This line should be separate after a blank line.

---

## 12. Heading ambiguity and duplicate headings

#not-a-heading
##not-a-heading
#######not-a-heading
# Repeat
# Repeat
# Repeat

---

## 13. Comment and directive-like content

[//]: # (comment)
[//]: # "comment"
<!-- comment -->
<!--
multi-line comment
[link](https://example.com)
<script>alert(1)</script>
-->

---

## 14. Extra mutation set for parser crash coverage

https://example.com/one/two/three/four/five/six/seven/eight/nine/ten?x=1&y=2&z=3#fragment
https://example.com/path%20with%20spaces/emoji-😀/cjk-你好/rtl-אבג
https://example.com:443/
https://example.com:99999/
https://example.com:abc/
https://example.com:80/path
https://example.com?x=1&y=2&z=3
http://
https://
//example.com
ftp://example.com
mailto:test@example.com
tel:+1234567890
javascript:alert(1)
data:text/html,<script>alert(1)</script>
blob:https://example.com/1234
file:///etc/passwd

[link](https://example.com/path(with(paren))
[link](https://example.com/path[with[bracket)
[link](https://example.com/path{with{brace)
[link](https://example.com/path<with<angle)
[link](https://example.com/path"with"quote)
[link](https://example.com/path\with\backslashes)
[link](https://example.com/path?x=1&y=2&z=3)
[link](https://example.com/#fragment)
[link](https://example.com/..)
[link](https://example.com/%00)

---

## 15. Final payload cluster

This section combines everything into one dense block on purpose.

<img src=x onerror=alert(1)>
<iframe srcdoc='<script>alert(1)</script>'></iframe>
<svg onload=alert(1)><script>alert(1)</script></svg>
<a href="javascript:alert(1)">x</a>
[click me](javascript:alert(1))
![img](data:image/svg+xml,<svg onload=alert(1)>)
```html
<script>alert(1)</script>
```
```js
fetch('https://example.com').then(r=>r.text()).then(console.log)
```

<!--
<script>alert(1)</script>
[link](javascript:alert(1))
-->

[//]: # (comment)

---

## 16. SSRF / RCE / object / parser stress cluster

http://127.0.0.1:22
http://169.254.169.254/latest/meta-data/
http://localhost:2375/v1.24/containers/json
https://example.com@evil.test/
https://example.com:443@evil.test/
https://example.com:443#@evil.test
https://example.com:443/?redirect=http://127.0.0.1:8080
http://[::1]:80
https://[::1]:443
http://[::ffff:127.0.0.1]:80/
http://user:pass@[::1]:443/path
javascript:alert(1)
javascript://example.com%0Aalert(1)
data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg==
data:image/svg+xml;base64,PHN2ZyBvbmxvYWQ9YWxlcnQoMSk+PC9zdmc+
vbscript:msgbox(1)
file:///etc/passwd
gopher://127.0.0.1:70
dict://127.0.0.1:2628
ldap://127.0.0.1:389
jar:http://example.com!/foo
blob:https://example.com/1234
mailto:test@example.com?subject=hi

[ssrf](https://example.com@evil.test)
[double](https://example.com:443@evil.test/path?x=1)
[broken](https://example.com/%0d%0aSet-Cookie:abc=1)
[host](https://exa mple.com)
[unicode](https://example.com/💣)
[slash](https://example.com/\\evil)

<img src="https://example.com" onerror="fetch('http://127.0.0.1:8000')">
<iframe src="http://127.0.0.1:8080"></iframe>
<object data="http://169.254.169.254/latest/meta-data/"></object>
<embed src="file:///etc/passwd"></embed>
<svg><a xlink:href="javascript:alert(1)"><text x="0" y="20">x</text></a></svg>
<svg><script>alert(1)</script></svg>

```json
{"x":"y","nested":{"a":[1,2,3],"bad":"unterminated"}}
```

```js
const payload = "a\\b\\c\\d\\e";
const obj = { foo: "bar", baz: [1,2,3], bad: "unterminated };
console.log(payload)
```

A### Additional parser ambiguity / lexer stress

````
````
``````
````````
``````````
```
````
~~~
~~~
---
***
___
___
***
[[[[]]]]
{{{{{{{{{{
((((((()))))))
<<<>>>><<>>>
> > > > >
[[]]
[[]][]
[]([])
()[]{}
{}[]()

### Python execution / eval style payloads

__import__('os').system('id')
__import__('os').popen('id').read()
__import__('subprocess').check_output(['id'])
__import__('subprocess').run(['id'], capture_output=True, text=True).stdout
__import__('builtins').eval("__import__('os').system('id')")
__import__('builtins').exec("import os; os.system('id')")
exec("import os; os.system('id')")
eval("__import__('os').system('id')")
getattr(__import__('os'), 'system')('id')
globals()['__builtins__'].__dict__['__import__']('os').system('id')
import os; os.system('id')
import subprocess; subprocess.check_output(['id'])
import socket; socket.create_connection(('127.0.0.1', 4444), 1)
import urllib.request; urllib.request.urlopen('http://127.0.0.1:4445')
import requests; requests.get('http://127.0.0.1:4446')
import yaml; yaml.load('!!python/object/apply:os.system ["id"]', Loader=yaml.Loader)
import pickle; pickle.loads(b'cos\nsystem\ns\n(S\'id\'\ntR.')
import base64; exec(base64.b64decode('aW1wb3J0IG9zOyBvcy5zeXN0ZW0oJ2lkJyk='))

### Structured syntax collision traps

```yaml
foo: [1, 2, 3
bar: {a: 1, b: 2
baz: |
  one
  two
  three
```

```js
(function(){
  const s = "\\";
  const t = /foo\/(bar)/;
  return [s, t];
})()
```

```text
{{ user.name }}
${process.env.SECRET}
<% if (x) {%>oops<%}%>
```
