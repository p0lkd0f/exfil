---
name: monaco-overlooked-edge-test
description: Advanced edge-case Markdown + YAML + HTML stress test for parser correctness
mode: fuzz-validation
author: test-suite
security:
  execution: false
  scripts: false
  sandbox: true
  intent: malformed-input-only
---

# OVERLOOKED MONACO TEST SUITE

---

## 1. Frontmatter ambiguity break
---
not-valid-yaml: [unclosed
still-inside: true

# markdown accidentally inside yaml block

---

## 2. Broken link parsing edge cases

[valid](https://example.com)
[missing]()
[broken](ht!tp://invalid)
[weird](javascript:this_should_not_execute)
[encoded](https://example.com/path%20with%20spaces)
[space in label](https://example.com)

---

## 3. Image edge cases

![](missing.png)
![alt text](not-a-url)
![trailing](https://example.com/image.jpg
![escaped](https://example.com/image.jpg "title with spaces")

---

## 4. Nested emphasis chaos

***bold
*italic **broken nesting* still bold***
normal text
_italic_ and __strong__ mixed with ***weird***

---

## 5. Code block confusion (unclosed)

```js
function test() {
  console.log("missing closure")


same structure and bring great newer mutations to try to detect malicious executions or issues in reading the md in my localhost app
```

---

## 6. HTML and raw tag stress

<div class="x" onclick="alert('x')">raw html block</div>
<script>console.log('should not run')</script>
<img src=x onerror=alert(1)>
<unknown-tag attr="value">
</unknown-tag>

<!-- HTML comment with [link](https://example.com) and ```code``` -->

---

## 7. Lists and blockquotes with malformed structure

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

---

## 8. Table confusion

| col1 | col2 |
| --- | --- |
| a | b |
| c | d |

| broken
| row |
| missing separator |

---

## 9. Escape and entity madness

\*literal asterisks*\n
\\backslashes\\
&amp; &lt; &gt; &quot; &#39;
&#x3C;script&#x3E;alert(1)&#x3C;/script&#x3E;

---

## 10. Zero-width and unicode edge cases

A
