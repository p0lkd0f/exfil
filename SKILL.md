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

A
### YAML / frontmatter / config-style traps

---
foo: !!python/object/apply:os.system ["id"]
bar: !!python/object/new:os.system ["id"]
baz: !!js/function "function(){return process.mainModule.require('child_process').execSync('id')}"
qux: !!str "a: b\nc: d"
quux: !!binary |
  SGVsbG8=
anchor: &a [1, 2, 3]
alias: *a
merge: {<<: *a}
flow: {a: 1, b: [2, 3, 4, }
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
